> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [rcore-os.cn](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter8/3semaphore.html)

> Toggle table of contents sidebar

[Back to top](#)

Toggle table of contents sidebar

本节导读[#]( #id2 "永久链接至标题")
-----------------------

在上一节中，我们介绍了互斥锁的起因、使用和实现过程。通过互斥锁，可以让线程在临界区执行时，独占共享资源。然而，当我们需要一种线程间更灵活的同步访问需求，如要求同时最多只允许 N 个线程在临界区中访问共享资源，或让某个线程等待另外一个线程执行到某一阶段后再继续执行的同步过程等，互斥锁这种方式就有点力不从心了。

在本节中，将介绍功能更加强大和灵活的同步互斥机制 – 信号量 (Semaphore) 的设计思路，使用方式和在操作系统中的具体实现。可以看到，在实现信号量的时候可能会用到互斥锁和处理器提供的原子指令，因此它是一种更高级的同步互斥机制。

注解

**同步互斥**

**同步** (Synchronization) 和 **互斥** (Mutual Exclusion) 事实上是在多线程并发访问过程中出现的两种不同需求。同步指的是线程执行顺序上的一些约束，比如一个线程必须等待另一个线程执行到某个阶段之后才能继续向下执行；而互斥指的是多线程在访问共享资源的时候，同一时间最多只有一个线程能够在共享资源的临界区中。

同步和互斥的界限其实比较模糊。比如，互斥也可以看成是一种同步需求，即一个线程在进入临界区之前必须等待当前在临界区中的线程（如果存在）退出临界区；而对于一种特定的同步需求，线程间也往往需要某些共享状态，线程需要通过查看或修改这些共享状态来进入等待或唤醒等待的线程。为了能够正确访问这些共享状态，就需要互斥。所以，二者之间是一种 “你中有我，我中有你” 的关系，我们就将这两种需求统称为 **同步互斥** ，而将针对于这种需求比较通用的解决方案称之为 **同步互斥原语** （简称为 **同步原语** ，英文 Synchronization Primitives ）。

信号量的起源和基本思路[#]( #id3 "永久链接至标题")
------------------------------

1963 年前后，当时的数学家兼计算机科学家 Edsger Dijkstra 和他的团队为 Electrologica X8 开发一款名为 THE 多道程序的操作系统的时候，提出了 **信号量** (Semaphore) 的概念。信号量是一种同步原语，用一个变量或一种抽象数据类型实现，用于控制多个线程对共享资源的访问。1965 年，Dijkstra 发表了论文手稿 “Cooperating sequential processes” ，详细论述了在多个顺序代码执行流（在论文中被称为 Sequential Processes ）的并发执行过程中，如果没有约束机制则会产生不确定的执行结果。为此，他提出了信号量的设计思路，能够让这些松耦合的顺序代码执行流能够在同步需求的约束下正确并发执行。

信号量相比互斥锁是一种更为强大和灵活的同步原语。它用来描述这样一种同步需求：初始状态下，某种资源的可用数量为一个非负整数 。线程可以在某个时刻占用一个该种资源，并在使用过后将资源返还。然而，如果此时已经没有可用的资源了，也就是说所有这类资源都已经被其他线程占用了，那么当前线程就需要暂时进入等待，等待其他线程至少返回一个资源之后再重新尝试占用资源或是直接向下执行。可以结合现实中这样一个例子来形象理解信号量：考虑一个火车站只有两个站台，最多可以同时停放两列火车。那么就可以用一个 的信号量来描述站台资源。设想，当一列火车尝试进站时，它会尝试占用一个站台来停车。如果此时仍有空闲的站台，那么它会看到绿色的信号灯而顺利进站；否则，它会看到信号灯为红色，此时它需要在站外等待空出一个站台再进站。

信号量支持两种操作：P 操作（来自荷兰语中的 Proberen ，意为尝试）和 V 操作（来自荷兰语中的 Verhogen ，意为增加），其中 P 操作表示线程尝试占用一个资源，而与之匹配的 V 操作表示线程将占用的资源归还。P 操作和 V 操作也是基于阻塞 - 唤醒机制实现的。当进行 P 操作的时候，如果此时没有可用的资源，则当前线程会被阻塞；而进行 V 操作的时候，如果返还之后有了可用的资源，且此时有线程被阻塞，那么就可以考虑唤醒它们。从数据结构层面，信号量中存在一个整数变量表示当前资源的状态，同时还有一个阻塞队列保存所有被阻塞的线程。在信号量不同的实现中，整数变量的具体含义以及阻塞队列中的内容都是不同的。它们其实也属于所有线程都能访问到的共享资源，但是不用担心其互斥性。因为线程不会直接访问它们，而是只能通过 P 操作和 V 操作，操作系统保证这两个操作中包括整数变量的修改和阻塞 / 唤醒线程的整个流程是原子的。

一种信号量实现的伪代码如下所示：

```
1fn P(S) {
 2    if S >= 1
 3        // 如果还有可用资源，更新资源剩余数量 S
 4        S = S - 1;
 5        // 使用资源
 6    else
 7        // 已经没有可用资源
 8        // 阻塞当前线程并将其加入阻塞队列
 9        <block and enqueue the thread>;
10}
12fn V(S) {
13    if <some threads are blocked on the queue>
14        // 如果已经有线程在阻塞队列中
15        // 则唤醒这个线程
16        <unblock a thread>;
17    else
18        // 否则只需恢复 1 资源可用数量
19        S = S + 1;
20}
```

在上述实现中信号量中的整数变量 为非负整数。当 时，表示还有 个可用资源；当 时，表示没有可用资源且可能已经有线程被阻塞。显然 应该被初始化为 。这种情形下 P 操作和 V 操作的具体实现可以参考注释。注意，阻塞在 P 操作中的线程一经被唤醒就会立即进入临界区而不会检查此时是否有可用资源。这是因为 **进行 V 操作的线程直接将资源移交给了它唤醒的线程** ，于是此时并没有更新资源可用数量。

下面是另外一种信号量实现的伪代码：

```
1fn P(S) {
 2    S = S - 1;
 3    if 0 > S then
 4        // 阻塞当前线程并将其加入阻塞队列
 5        <block and enqueue the thread>;
 6}
 8fn V(S) {
 9    S = S + 1;
10    if <some threads are blocked on the queue>
11        // 如果已经有线程在阻塞队列中
12        // 则唤醒这个线程
13        <unblock a thread>;
14}
```

上述实现中，整数变量 的含义如下：当 时，表示还有 个可用资源；当 时，表示所有可用资源恰好耗尽；当 时，表示此时有 个线程被阻塞。显然 也应该被初始化为 。对于 P 操作，我们首先将 减一，如果发现 ，说明之前 ，一定没有可用资源了，于是需要阻塞当前线程；对于 V 操作，这里将 加一可以这样理解：如果此时没有线程被阻塞则恢复 1 个可用资源；否则将阻塞线程数减少 1 ，因为当前线程将资源移交给了其中一个被阻塞的线程并唤醒了它。

这只是信号量的两种不同实现，本质上是相同的。

信号量的使用方法[#]( #id4 "永久链接至标题")
---------------------------

信号量的初始资源可用数量 是一个非负整数，它决定了信号量的用途。如果 为大于 0 的任意整数，我们称之为计数信号量 (Counting Semaphore) 或者一般信号量 (General Semaphore) ，它可以用来进行上面描述过的那种资源管理。特别地，当 的时候，我们称其为二值信号量 (Binary Semaphore) 。从定义上容易看出它和互斥锁是等价的。因此，互斥锁可以看成信号量的一种特例。

然而，当 的时候，信号量就与资源管理无关了，而是可以用作一种比较通用的同步原语。比如，现在的需求是：线程 A 需要等待线程 B 执行到了某一阶段之后再向下执行。假设有一个 的信号量。那么，在线程 A 需要等待的时候可以对该信号量进行 P 操作，于是线程会被阻塞。在线程 B 执行完指定阶段之后再对该信号量进行 V 操作就能够唤醒线程 A 向下执行。如下图所示：

![](https://rcore-os.cn/rCore-Tutorial-Book-v3/_images/semaphore-sync.png)

在线程 A 和 B 上分别对一个 的信号量使用 P 操作和 V 操作即可保证：在线程 B 的代码块 1 执行 **完毕** 之后才会 **开始** 执行线程 A 的代码块 2 。换句话说，在线程 A 的代码块 2 的开头就可以假定此前线程 B 的代码块 1 已经执行完毕了。这在很多情况下是一种很有用的同步约束关系。

注解

**唤醒丢失问题**

在上面线程 A 和 B 的同步问题中，其实未必总先是线程 A 执行到 P 操作被阻塞住，然后线程 B 执行 V 操作唤醒线程 A 。也有另一种可能是线程 B 先执行 V 操作，随后线程 A 再执行 P 操作。那么在线程 B 执行 V 操作的时候，信号量的阻塞队列中是没有任何线程的，此时 B 无法唤醒 A 。但如果此时 B 什么都不做，那么之后 A 执行 P 操作陷入阻塞的时候就没有任何线程能够唤醒 A 了，这将导致 A 无法顺利执行。

这种问题被我们称为 **唤醒丢失** (Lost Wakeup) 问题。为了解决这个问题，我们需要 B 在进行 V 操作的时候即使没有线程需要唤醒，也需要一种方法将这次可能的唤醒记录下来。请同学思考我们在上面的信号量实现中是如何解决这个问题的。

信号量的系统调用接口[#]( #id5 "永久链接至标题")
-----------------------------

和互斥锁一样，我们将信号量也视为进程内的一种由操作系统管理并由进程内所有线程共享的资源。同个进程内可以有多个不同信号量，它们之间通过信号量 ID （与互斥锁或其他资源的的 ID 独立）来区分。相关系统调用接口如下：

```
1/// 功能：为当前进程新增一个信号量。
 2/// 参数：res_count 表示该信号量的初始资源可用数量，即 N ，为一个非负整数。
 3/// 返回值：假定该操作必定成功，返回创建的信号量的 ID 。
 4/// syscall ID : 1020
 5pub fn sys_semaphore_create(res_count: usize) -> isize;
 6/// 功能：对当前进程内的指定信号量进行 V 操作。
 7/// 参数：sem_id 表示要进行 V 操作的信号量的 ID 。
 8/// 返回值：假定该操作必定成功，返回 0 。
 9pub fn sys_semaphore_up(sem_id: usize) -> isize;
10/// 功能：对当前进程内的指定信号量进行 P 操作。
11/// 参数：sem_id 表示要进行 P 操作的信号量的 ID 。
12/// 返回值：假定该操作必定成功，返回 0 。
13pub fn sys_semaphore_down(sem_id: usize) -> isize;
```

可以看到，这里我们分别用 down 和 up 这样比较形象的名字作为 P 操作和 V 操作的别名，因为 P 操作和 V 操作通常分别导致整数变量 的降低和增加。这几个系统调用也会在用户库 `user_lib` 被直接封装为 `semaphore_create/down/up` 。

信号量的应用[#]( #id6 "永久链接至标题")
-------------------------

这里给出两个应用：第一个是信号量作为同步原语来解决条件同步问题；第二个则是生产者和消费者基于一个有限缓冲进行协作的复杂问题。

### 条件同步问题[#]( #link -cond-sync "永久链接至标题")

来看这样一个例子：

```
1// user/src/bin/sync_sem.rs
 3const SEM_SYNC: usize = 0;
 5unsafe fn first() -> ! {
 6    sleep(10);
 7    println!("First work and wakeup Second");
 8    semaphore_up(SEM_SYNC);
 9    exit(0)
10}
12unsafe fn second() -> ! {
13    println!("Second want to continue,but need to wait first");
14    semaphore_down(SEM_SYNC);
15    println!("Second can work now");
16    exit(0)
17}
19#[no_mangle]
20pub fn main() -> i32 {
21    // create semaphores
22    assert_eq!(semaphore_create(0) as usize, SEM_SYNC);
23    // create threads
24    let threads = vec![
25        thread_create(first as usize, 0),
26        thread_create(second as usize, 0),
27    ];
28    // wait for all threads to complete
29    for thread in threads.iter() {
30        waittid(*thread as usize);
31    }
32    println!("sync_sem passed!");
33    0
34}
```

其中，两个线程并发执行，第一个线程执行 `first` 函数而第二个线程执行 `second` 函数。我们想要达到的同步约束是：在第二个线程执行 `second` 的后一行打印之前，第一个线程必须完成 `first` 中的打印。于是，根据上面关于信号量用法的介绍，第 22 行我们调用 `semaphore_create` 函数创建一个用于同步的 的信号量，其 ID 应为 `SEM_SYNC` 。为了实现同步约束，则只需第一个线程在 `first` 中的打印结束后进行信号量的 V（也就是 up） 操作，而第二个线程在 `second` 后一次打印之前进行信号量的 P （也就是 down）操作，这样即可解决这个问题。

### 生产者和消费者问题[#]( #id8 "永久链接至标题")

![](https://rcore-os.cn/rCore-Tutorial-Book-v3/_images/mpsc-problem.png)

生产者 - 消费者问题（也称为有限缓冲问题）是 Dijkstra 自 1965 年以来描述的一系列同步互斥问题中的一个。如图所示，一共有 5 个线程在同进程下进行协作，其中有 4 个生产者（Producer，图中右侧）和 1 个消费者（Consumer，图中左侧），它们共享一个容量有限的环形缓冲区（图中间）。生产者的职责是将输入放入缓冲区，而消费者则从缓冲区中取出数据进行处理。然而，这两种操作并不总是能够立即成功的。比如，当缓冲区已满的情况下，生产者就无法将数据放入缓冲区，需要等消费者取出数据空出缓冲区槽位；而当缓冲区为空没有数据的时候，消费者也无法从中取出数据，需要等生产者将数据填充到缓冲区。考虑使用信号量来实现上述同步需求，可以看成管理以下资源：

*   空闲槽位资源，初始数量 等于缓冲区容量。生产者每次写入需要占用 1 个，消费者每次读取恢复 1 个；
    
*   可用数据资源，初始数量 （最开始缓冲区为空）。消费者每次读取占用 1 个，生产者每次写入恢复 1 个；
    
*   将缓冲区以及相应指针（即 `front` 和 `tail` ）整体上视作一种共享资源，那么生产者和消费者的写入和读取都会对这个共享资源进行修改。注意 **信号量只保证无可用资源时进行阻塞，但并不保证访问共享资源的互斥性，甚至这可能是两种不同资源** 。因此，我们还需要引入互斥锁对缓冲区进行保护，这里使用一个 的二值信号量来实现。
    

代码如下：

```
1// user/src/bin/mpsc_sem.rs
 3const SEM_MUTEX: usize = 0;
 4const SEM_EMPTY: usize = 1;
 5const SEM_AVAIL: usize = 2;
 6const BUFFER_SIZE: usize = 8;
 7static mut BUFFER: [usize; BUFFER_SIZE] = [0; BUFFER_SIZE];
 8static mut FRONT: usize = 0;
 9static mut TAIL: usize = 0;
10const PRODUCER_COUNT: usize = 4;
11const NUMBER_PER_PRODUCER: usize = 100;
13unsafe fn producer(id: *const usize) -> ! {
14    let id = *id;
15    for _ in 0..NUMBER_PER_PRODUCER {
16        semaphore_down(SEM_EMPTY);
17        semaphore_down(SEM_MUTEX);
18        BUFFER[TAIL] = id;
19        TAIL = (TAIL + 1) % BUFFER_SIZE;
20        semaphore_up(SEM_MUTEX);
21        semaphore_up(SEM_AVAIL);
22    }
23    exit(0)
24}
26unsafe fn consumer() -> ! {
27    for _ in 0..PRODUCER_COUNT * NUMBER_PER_PRODUCER {
28        semaphore_down(SEM_AVAIL);
29        semaphore_down(SEM_MUTEX);
30        print!("{} ", BUFFER[FRONT]);
31        FRONT = (FRONT + 1) % BUFFER_SIZE;
32        semaphore_up(SEM_MUTEX);
33        semaphore_up(SEM_EMPTY);
34    }
35    println!("");
36    exit(0)
37}
39#[no_mangle]
40pub fn main() -> i32 {
41    // create semaphores
42    assert_eq!(semaphore_create(1) as usize, SEM_MUTEX);
43    assert_eq!(semaphore_create(BUFFER_SIZE) as usize, SEM_EMPTY);
44    assert_eq!(semaphore_create(0) as usize, SEM_AVAIL);
45    // create threads
46    let ids: Vec<_> = (0..PRODUCER_COUNT).collect();
47    let mut threads = Vec::new();
48    for i in 0..PRODUCER_COUNT {
49        threads.push(thread_create(
50            producer as usize,
51            &ids.as_slice()[i] as *const _ as usize,
52        ));
53    }
54    threads.push(thread_create(consumer as usize, 0));
55    // wait for all threads to complete
56    for thread in threads.iter() {
57        waittid(*thread as usize);
58    }
59    println!("mpsc_sem passed!");
60    0
61}
```

第 42-44 行分别创建了二值信号量 `SEM_MUTEX` ，描述空闲槽位资源的信号量 `SEM_EMPTY` 以及描述可用数据资源的信号量 `SEM_AVAIL` 。生产者线程会执行 `producer` 函数，循环的每次迭代向共享缓冲区写入数据。于是在写入之前需要进行信号量 `SEM_EMPTY` 的 down 操作尝试占用一个空闲槽位资源，而在写入之后进行信号量 `SEM_AVAIL` 的 up 操作释放一个可用数据资源。相对的，消费者线程会执行 `consumer` 函数，循环的每次迭代从共享缓冲区读取数据。于是在读入之前需要进行信号量 `SEM_AVAIL` 的 down 操作尝试占用一个可用数据资源，而在读取之后进行信号量 `SEM_EMPTY` 的 up 操作释放一个空闲槽位资源。两个线程对共享缓冲区的操作都需要用二值信号量 `SEM_MUTEX` 来保护。

从这个例子可以看出，信号量的使用可以是非常灵活的。同一个信号量的 P 操作和 V 操作不一定是连续的，甚至可以不在一个线程上。

提示

请同学们思考：能否将二值信号量的 down 和 up 操作放在循环每次迭代的最外层？为什么？

实现信号量[#]( #id9 "永久链接至标题")
------------------------

现在我们来看操作系统如何实现信号量相关的系统调用。首先，和互斥锁一样，信号量也是进程内的一种资源，而且同一个进程内也可以有多个不同的信号量，我们可以通过它们的组合来实现更加复杂的同步需求。因此，从数据结构角度看，需要将一个信号量表加入到进程控制块 PCB 中：

```
1// os/src/task/process.rs
3pub struct ProcessControlBlockInner {
4    ...
5    pub mutex_list: Vec<Option<Arc<dyn Mutex>>>,
6    pub semaphore_list: Vec<Option<Arc<Semaphore>>>,
7    ...
8}
```

在内核中，信号量是一个叫做 `Semaphore` 内核数据结构，定义如下：

```
1// os/src/sync/semaphore.rs
 3pub struct Semaphore {
 4    pub inner: UPSafeCell<SemaphoreInner>,
 5}
 7pub struct SemaphoreInner {
 8    pub count: isize,
 9    pub wait_queue: VecDeque<Arc<TaskControlBlock>>,
10}
12impl Semaphore {
13    pub fn new(res_count: usize) -> Self {...}
14    pub fn up(&self) {...}
15    pub fn down(&self) {...}
16}
```

其中包括一个整数变量 `count` （也就是上面信号量基本思路中的整数变量 S）以及保存阻塞在该信号量中的所有线程的阻塞队列 `wait_queue` 。信号量 `Semaphore` 支持三种操作：创建 `new` （带有一个参数 `res_count` ，也即信号量初始可用资源数量 ）以及 up （也即 V 操作）和 down （也即 P 操作）。相关的系统调用主要是找到当前进程中的指定信号量实例，随后再调用它的这几种方法来实现的，在此不再赘述。于是我们主要看这几种方法是如何实现的：

```
1// os/src/sync/semaphore. rs
 3impl Semaphore {
 4    pub fn new (res_count: usize) -> Self {
 5        Self {
 6            inner: unsafe {
 7                UPSafeCell:: new (SemaphoreInner {
 8                    count: res_count as isize,
 9                    wait_queue: VecDeque:: new (),
10                })
11            },
12        }
13    }
15    pub fn up (&self) {
16        let mut inner = self. inner. exclusive_access ();
17        inner. count += 1;
18        if inner. count <= 0 {
19            if let Some (task) = inner. wait_queue. pop_front () {
20                wakeup_task (task);
21            }
22        }
23    }
25    pub fn down (&self) {
26        let mut inner = self. inner. exclusive_access ();
27        inner. count -= 1;
28        if inner. count < 0 {
29            inner. wait_queue. push_back (current_task (). unwrap ());
30            drop (inner);
31            block_current_and_run_next ();
32        }
33    }
34}
```

`new` 方法比较简单。而 `up` 和 `down` 方法和我们在信号量基本思路中介绍的 [信号量的第二种实现](#link-the-second-sem-impl) 一致。只需要注意如何使用阻塞 - 唤醒机制的核心接口 `block_current_and_run_next` 和 `wakeup_task` 即可。

小结[#]( #id10 "永久链接至标题")
----------------------

本节我们介绍了相比互斥锁更加灵活强大的同步原语——信号量，并用它解决了条件同步和经典的生产者 - 消费者问题。但是要看到的是，信号量还是比较复杂的。对于程序员来说开发和阅读代码比较困难，且比较容易出错，对程序员的要求比较高。

参考文献[#]( #id11 "永久链接至标题")
------------------------

*   Dijkstra, Edsger W. Cooperating sequential processes (EWD-123) (PDF). E.W. Dijkstra Archive. Center for American History, University of Texas at Austin. (transcription) (September 1965) [https://www.cs.utexas.edu/users/EWD/transcriptions/EWD01xx/EWD123.html](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD01xx/EWD123.html)
    
*   Downey, Allen B. (2016) [2005]. “The Little Book of Semaphores” (2nd ed.). Green Tea Press.
    
*   Leppäjärvi, Jouni (May 11, 2008). “A pragmatic, historically oriented survey on the universality of synchronization primitives” (pdf). University of Oulu, Finland.
    
*   [Producer-consumer problem, Wikipedia](https://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem)