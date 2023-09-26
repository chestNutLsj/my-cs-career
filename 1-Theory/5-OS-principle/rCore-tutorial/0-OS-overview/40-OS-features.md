基于操作系统的四个抽象，我们可以看出，从总体上看，操作系统具有五个方面的特征：
1. 虚拟化 (Virtualization)、
2. 并发性 (Concurrency)、
3. 异步性、
4. 共享性、
5. 持久性 (Persistency)。

操作系统的虚拟化可以理解为它对内存、CPU 的抽象和处理；并发性和共享性可以理解为操作系统支持多个应用程序 “同时” 运行；异步性可以从操作系统调度、中断处理对应用程序执行造成的影响等几个方面来理解；持久性则可以从操作系统中的文件系统支持把数据方便地从磁盘等存储介质上存入和取出来理解。

## 虚拟性
### 内存虚拟化

首先来看看内存虚拟化。程序员在写应用程序的时候，不用考虑其程序的起始内存地址要放到计算机内存的具体某个位置，而是用字符串符号定义了各种变量和函数，直接在代码中便捷地使用这些符号就行了。==这是由于操作系统建立了一个 _地址固定_ ， _空间巨大_ 的虚拟内存给应用程序来运行，这是 **内存虚拟化** ==。内存虚拟化的核心问题是：采用什么样的方式让虚拟地址和物理地址对应起来，也就是如何将虚拟地址 “翻译” 成物理地址。

**内存虚拟化** 其实是一种 **“空间虚拟化”** ，可进一步细分为 **内存地址虚拟化** 和 **内存大小虚拟化** 。编写的源代码中，每个符号在运行时都需要对应到具体的内存地址。这些内存地址的具体数值是什么？程序员不用关心。为什么？因为编译器会自动帮我们把这些符号翻译成地址，形成可执行程序。程序使用的内存是否占得太大了？在一般情况下，程序员也不用关心。

>[! note] 回顾虚拟地址
>
>实际上，编译器 (Compiler，比如 gcc) 和链接器 (linker，比如 ld) 也不知道程序每个符号对应的地址应该放在未来程序运行时的哪个物理内存地址中。所以，编译器的一个简单处理办法就是，设定一个固定地址（比如 0x10000）作为起始地址开始存放代码，代码之后是数据，所有变量和函数的符号都在这个起始地址之后的某个固定偏移位置。假定程序每次运行都是位于一个不会变化的起始地址。这里的变量指的是全局变量，其地址在编译链接后会确定不变。但局部变量是放在堆栈中的，会随着堆栈大小的动态变化而变化。这里编译器产生的地址就是虚拟地址。
>
>这样一来，由于编译器和链接器采用固定地址+偏移的‘偷懒’策略，源代码中内容从虚拟内存地址到计算机物理内存地址的映射的重担，将由操作系统一力承担。操作系统会把编译器和链接器生成的执行代码和数据放到空闲的物理内存中，并建立虚拟地址到物理地址的映射关系。由于物理内存中的空闲区域是动态变化的，这导致虚拟地址到物理地址的映射关系也是动态变化的，需要操作系统来维护好可变的映射关系，确保编译器 “固定起始地址” 的假设成立。只有操作系统维护好了这个映射关系，才能让程序员只需写一些易于人理解的字符串符号来代表一个内存空间地址。这样，编译器只需确定一个固定地址作为程序的起始地址，就可以不用考虑将来这个程序要在哪个物理地址空间运行的问题，从而实现了 **内存地址虚拟化** 。

应用程序在运行时不用考虑当前物理内存是否够用。如果应用程序需要一定空间的内存，但由于在某些情况下，物理内存的空闲空间可能不多了，这时操作系统通过把物理内存中最近没使用的空间（不是空闲的，只是最近用得少）中的数据换出（就是 “挪地”）到硬盘上暂时存储起来，这样空闲空间就大了，就可以满足应用程序的运行时内存需求了，从而实现了 **内存大小虚拟化** 。

### CPU 虚拟化

再来看 CPU 虚拟化。不同的应用程序可以在内存中并发运行，相同的应用程序也可有多个拷贝在内存中并发运行。而每个程序都 “认为” 自己完全独占了 CPU 在运行，这是 **“CPU 虚拟化”**，也是一种 **“时间虚拟化”** 。

操作系统给了运行的应用程序一个幻觉，即操作系统把时间分成小段，每个应用程序占用其中一小段时间片运行，用完这一时间片后，操作系统会切换到另外一个应用程序，让它运行。由于时间片很短，操作系统的切换开销也很小，应用程序或使用应用程序的用户基本上是看不出的，反而感觉到多个程序各自在独立 “并行” 执行，从而实现了 **CPU 虚拟化** 。

## 并发性

操作系统为了能够让 CPU 充分地忙起来，并充分利用各种资源，就需要有多种不同的应用程序在执行。这些应用程序是分时执行的，并由操作系统来完成各个应用在运行时的任务切换。

并发性虽然能有效改善系统资源的利用率，但也带来了对共享资源的争夺问题，即同步互斥问题。还会带来执行时间的不确定性问题，即并发程序在执行中是走走停停，断续推进的，使得应用程序的完成时间是不确定的。并发性对操作系统的设计也带来了很多挑战，一不小心就会出现程序执行结果不确定，程序死锁等很难调试和重现的问题。

> [! note] 区分并发和并行
> * 并行 (Parallel) 是指两个或者多个事件在同一时刻发生
> * 并发 (Concurrent) 是指两个或多个事件在同一时间间隔内发生。

对于基于单 CPU 的计算机而言，各个 “同时” 运行的程序其实是串行分时复用一个 CPU ，任一个时刻点上只有一个程序在 CPU 上运行。这些虚拟性的特征给应用程序的开发和执行提供了非常方便的执行环境，但也给操作系统的设计与实现提出了很多挑战。

## 异步性

在这里，==异步是指由于操作系统的调度和中断等，会不时地暂停或打断当前正在运行的程序，使得程序的整个运行过程走走停停==。在应用程序运行的表现上，特别体现在它的执行完成时间是不可预测的。但需要注意，只要应用程序的输入是一致的，那么它的输出结果应该是符合预期的。（如果不符合预期，那么说明程序的并发互斥上存在漏洞，需要修复）

## 共享性

共享是指多个应用并发运行时，宏观上体现出它们可同时访问同一个资源，即这个资源可被共享。但其实在微观上，操作系统在硬件等的支持下要确保应用程序互斥访问这个共享的资源。

比如，
- 在单核处理器下，对于两个应用同时访问同一个内存单元的情况，从宏观的应用层面上看，二者都能正确地读出同一个内存单元的内容；而在微观上，操作系统会调度应用程序的先后执行顺序，确保在任何一个时刻，只有一个应用去访问存储单元。

- 在多核处理器下，多个 CPU 核可能同时访问同一内存单元，在这种多核场景下的共享性不仅仅由 OS 来保证，还需硬件级的 Cache 一致性保证。

## 持久性

操作系统提供了文件系统来从可持久保存的存储介质（磁盘， SSD 等，以后以硬盘来代表）中取数据和代码到内存中，并可以把内存中的数据写回到硬盘上。硬盘在这里是外设，具有持久性，以文件系统的形式呈现给应用程序。

> [! note] 文件系统是存储外设的虚拟化。
> 
> ==文件系统也可看成是操作系统对存储外设（如硬盘、SSD 等）的虚拟化==。这种持久性的特征进一步带来了共享属性，即在文件系统中的文件可以被多个运行的程序所访问，从而给应用程序之间实现数据共享提供了方便。即使掉电，存储外设上的数据还不会丢失，可以在下一次机器加电后提供给运行的程序使用。持久性对操作系统的执行效率提出了挑战，如何让数据在高速的内存和慢速的硬盘间高效流动是需要操作系统考虑的问题。