## 本节导读

现在我们对如何了解计算机系统中给的外设信息，以及如何初始化或与外设进行交互有了基本的了解。接下来，我们需要看看如何在完整的操作系统中通过添加设备驱动程序来扩展应用访问外设的 I/O 能力。本节将通过介绍一个具体的物理设备串口的驱动程序的设计与实现，来分析如何在操作系统中添加设备驱动程序。

串口驱动程序[#]( #id3 "永久链接至标题")
-------------------------

我们要管理是串口 (UART) 物理设备。我们在第一章其实就接触了串口，但当时是通过 RustSBI 来帮 OS 完成对串口的访问，即 OS 只需发出两种 SBI 调用请求就可以输出和获取字符了。但这种便捷性是有代价的。比如 OS 在调用获取字符的 SBI 调用请求后，RustSBI 如果没收到串口字符，会返回 `-1` ，这样 OS 只能采用类似轮询的方式来继续查询。到第七章为止的串口驱动不支持中断是导致在多进程情况下，系统效率低下的主要原因之一。大家也不要遗憾，我们的第一阶段的目标是 **Just do it** ，先把 OS 做出来。在本节，我们需要逐步改进优化对串口的操作了。

串口（Universal Asynchronous Receiver-Transmitter，简称 UART）是一种在嵌入式系统中常用的用于传输、接收系列数据的外部设备。串行数据传输是逐位（bit）顺序发送数据的过程。了解 QEMU 模拟的兼容 NS16550A 硬件规范 [1](#uart1) [2](#uart2) 是写驱动程序的准备工作，建议同学们先阅读一下。每个 UART 使用 8 个 I/O 字节来访问其寄存器。下表 [1](#uart1) 显示了 UART 中每个寄存器的地址和基本含义。表中使用的 base 是串口设备的起始地址。在 QEMU 模拟的 virt 计算机中串口设备寄存器的 MMIO 起始地址为 `0x10000000` 。

<table><colgroup><col> <col> <col> <col> <col> </colgroup><thead><tr><th><p>I/O port</p></th><th><p>Read (DLAB=0)</p></th><th><p>Write (DLAB=0)</p></th><th><p>Read (DLAB=1)</p></th><th><p>Write (DLAB=1)</p></th></tr></thead><tbody><tr><td><p>base</p></td><td><p><strong>RBR</strong> receiver buffer</p></td><td><p><strong>THR</strong> transmitter holding</p></td><td><p><strong>DLL</strong> divisor latch LSB</p></td><td><p><strong>DLL</strong> divisor latch LSB</p></td></tr><tr><td><p>base+1</p></td><td><p><strong>IER</strong> interrupt enable</p></td><td><p><strong>IER</strong> interrupt enable</p></td><td><p><strong>DLM</strong> divisor latch MSB</p></td><td><p><strong>DLM</strong> divisor latch MSB</p></td></tr><tr><td><p>base+2</p></td><td><p><strong>IIR</strong> interrupt identification</p></td><td><p><strong>FCR</strong> FIFO control</p></td><td><p><strong>IIR</strong> interrupt identification</p></td><td><p><strong>FCR</strong> FIFO control</p></td></tr><tr><td><p>base+3</p></td><td><p><strong>LCR</strong> line control</p></td><td><p><strong>LCR</strong> line control</p></td><td><p><strong>LCR</strong> line control</p></td><td><p><strong>LCR</strong> line control</p></td></tr><tr><td><p>base+4</p></td><td><p><strong>MCR</strong> modem control</p></td><td><p><strong>MCR</strong> modem control</p></td><td><p><strong>MCR</strong> modem control</p></td><td><p><strong>MCR</strong> modem control</p></td></tr><tr><td><p>base+5</p></td><td><p><strong>LSR</strong> line status</p></td><td><p><em>factory test</em></p></td><td><p><strong>LSR</strong> line status</p></td><td><p><em>factory test</em></p></td></tr><tr><td><p>base+6</p></td><td><p><strong>MSR</strong> modem status</p></td><td><p><em>not used</em></p></td><td><p><strong>MSR</strong> modem status</p></td><td><p><em>not used</em></p></td></tr><tr><td><p>base+7</p></td><td><p><strong>SCR</strong> scratch</p></td><td><p><strong>SCR</strong> scratch</p></td><td><p><strong>SCR</strong> scratch</p></td><td><p><strong>SCR</strong> scratch</p></td></tr></tbody></table>

注：LCR 寄存器中的 DLAB 位设置为 0 或 1 ，会导致 CPU 访问的是不同的寄存器。比如，DLAB 位为 0 时，读位于 base 处的串口寄存器是 RBR， DLAB 位为 1 时，读位于 base 处的串口寄存器是 DLL。

大致猜测完上述寄存器的含义后，我们就算是完成前期准备工作，在接下来的驱动程序设计中，会用到上述的部分寄存器。 我们先尝试脱离 RustSBI 的帮助，在操作系统中完成支持中断机制的串口驱动。

通过查找 `dtc` （Device Tree Compiler）工具生成的 `riscv64-virt.dts` 文件，我们可以看到串口设备相关的 MMIO 模式的寄存器信息和中断相关信息。

```
...
chosen {
  bootargs = [00];
  stdout-path = "/uart@10000000";
};

uart@10000000 {
  interrupts = <0x0a>;
  interrupt-parent = <0x02>;
  clock-frequency = <0x384000>;
  reg = <0x00 0x10000000 0x00 0x100>;
  compatible = "ns16550a";
};
```

`chosen` 节点的内容表明字符输出会通过串口设备打印出来。`uart@10000000` 节点表明串口设备中寄存器的 MMIO 起始地址为 `0x10000000` ，范围在 `0x00~0x100` 区间内，中断号为 `0x0a` 。 `clock-frequency` 表示时钟频率，其值为 0x38400 ，即 3.6864 MHz。 `compatible = "ns16550a"` 表示串口的硬件规范兼容 NS16550A。

在如下情况下，串口会产生中断：

*   有新的输入数据进入串口的接收缓存
    
*   串口完成了缓存中数据的发送
    
*   串口发送出现错误
    

这里我们仅关注有输入数据时串口产生的中断。

在 UART 中，可访问的 I/O 寄存器一共有 8 个。访问 I/O 寄存器的方法把串口寄存器的 MMIO 起始地址加上偏移量，就是各个寄存器的 MMIO 地址了。

### 串口设备初始化[#]( #id7 "永久链接至标题")

对串口进行初始化设置的相关代码如下所示：

```
1// os/src/drivers/chardev/mod.rs
 2...
 3lazy_static! {
 4   pub static ref UART: Arc<CharDeviceImpl> = Arc::new(CharDeviceImpl::new());
 5}
 6// os/src/boards/qemu.rs
 7pub type CharDeviceImpl = crate::drivers::chardev::NS16550a<VIRT_UART>;
 8// os/src/drivers/chardev/ns16550a.rs
 9impl<const BASE_ADDR: usize> NS16550a<BASE_ADDR> {
10   pub fn new() -> Self {
11      let mut inner = NS16550aInner {
12            ns16550a: NS16550aRaw::new(BASE_ADDR),
13            read_buffer: VecDeque::new(),
14      };
15      inner.ns16550a.init();
16      Self {
17            inner: unsafe { UPIntrFreeCell::new(inner) },
18            condvar: Condvar::new(),
19      }
20   }
21}
22...
23impl NS16550aRaw {
24   pub fn init(&mut self) {
25      let read_end = self.read_end();
26      let mut mcr = MCR::empty();
27      mcr |= MCR::DATA_TERMINAL_READY;
28      mcr |= MCR::REQUEST_TO_SEND;
29      mcr |= MCR::AUX_OUTPUT2;
30      read_end.mcr.write(mcr);
31      let ier = IER::RX_AVAILABLE;
32      read_end.ier.write(ier);
33   }
34}
```

上述代码完成的主要工作包括：

### 串口设备输入输出操作[#]( #id8 "永久链接至标题")

先看串口输出，由于不设置和处理输出后产生中断的情况，使得整个输出操作比较简单。即向偏移量为 `0` 的串口控制寄存器的 MMIO 地址写 8 位字符即可。

```
1// os/src/drivers/chardev/ns16550a.rs
 3impl<const BASE_ADDR: usize> CharDevice for NS16550a<BASE_ADDR> {
 4   fn write(&self, ch: u8) {
 5      let mut inner = self.inner.exclusive_access();
 6      inner.ns16550a.write(ch);
 7   }
 8impl NS16550aRaw {
 9   pub fn write(&mut self, ch: u8) {
10      let write_end = self.write_end();
11      loop {
12            if write_end.lsr.read().contains(LSR::THR_EMPTY) {
13               write_end.thr.write(ch);
14               break;
15            }
16      }
17   }
```

在以往的操作系统实现中，当一个进程通过 `sys_read` 系统调用来获取串口字符时，并没有用上中断机制。但一个进程读不到字符的时候，将会被操作系统调度到就绪队列的尾部，等待下一次执行的时刻。这其实就是一种变相的轮询方式来获取串口的输入字符。这里其实是可以对进程管理做的一个改进，来避免进程通过轮询的方式检查串口字符输入。既然我们已经在上一章设计实现了让用户态线程挂起的同步互斥机制，我们就可以把这种机制也用在内核中，在外设不能及时提供资源的情况下，让想获取资源的线程或进程挂起，直到外设提供了资源，再唤醒线程或进程继续执行。

目前，支持中断的驱动可有效地支持等待的进程唤醒的操作。以串口为例，如果一个进程通过系统调用想获取串口输入，但此时串口还没有输入的字符，那么操作系统就设置一个进程等待串口输入的条件变量（条件变量包含一个等待队列），然后把当前进程设置等待状态，并挂在这个等待队列上，再把 CPU 让给其它就绪进程执行。对于串口输入的处理，由于要考虑中断，相对就要复杂一些。读字符串的代码如下所示：

```
1//os/src/fs/stdio.rs
 2impl File for Stdin {
 3   ...
 4   fn read(&self, mut user_buf: UserBuffer) -> usize {
 5      assert_eq!(user_buf.len(), 1);
 6      //println!("before UART.read() in Stdin::read()");
 7      let ch = UART.read();
 8      unsafe {
 9            user_buf.buffers[0].as_mut_ptr().write_volatile(ch);
10      }
11      1
12   }
13// os/src/drivers/chardev/ns16550a.rs
14impl<const BASE_ADDR: usize> CharDevice for NS16550a<BASE_ADDR> {
15   fn read(&self) -> u8 {
16      loop {
17            let mut inner = self.inner.exclusive_access();
18            if let Some(ch) = inner.read_buffer.pop_front() {
19               return ch;
20            } else {
21               let task_cx_ptr = self.condvar.wait_no_sched();
22               drop(inner);
23               schedule(task_cx_ptr);
24            }
25      }
26   }
```

响应串口输入中断的代码如下所示：

```
1// os/src/boards/qemu.rs
 2pub fn irq_handler() {
 3   let mut plic = unsafe { PLIC:: new (VIRT_PLIC) };
 4   let intr_src_id = plic.claim (0, IntrTargetPriority::Supervisor);
 5   match intr_src_id {
 6      ...
 7      10 => UART. handle_irq (),
 8   }
 9   plic.complete (0, IntrTargetPriority:: Supervisor, intr_src_id);
10}
11// os/src/drivers/chardev/ns16550a. rs
12impl<const BASE_ADDR: usize> CharDevice for NS16550a<BASE_ADDR> {
13   fn handle_irq (&self) {
14      let mut count = 0;
15      self. inner. exclusive_session (|inner| {
16            while let Some (ch) = inner.ns16550a.read () {
17               count += 1;
18               inner. read_buffer. push_back (ch);
19            }
20      });
21      if count > 0 {
22            self.condvar.signal ();
23      }
24   }
```

对于操作系统的一般处理过程是，首先是能接收中断，即在 `trap_handler` 中通过访问 `scause` 寄存器，能够识别出有外部中断产生。然后再进一步通过读 PLIC 的 `Claim` 寄存器来了解是否是收到了串口发来的输入中断。如果 PLIC 识别出是串口，就会调用串口的中断处理例程。 当产生串口有输入并产生中断后，操作系统通过对偏移量为 `0` 的串口寄存器的进行读操作，从而获得通过串口输入的字符，并存入 `NS16550aInner::read_buffer` 中。然后操作系统将查找等待串口输入的等待队列上的进程，把它唤醒并加入到就绪队列中。这样但这个进程再次执行时，就可以获取到串口数据了。