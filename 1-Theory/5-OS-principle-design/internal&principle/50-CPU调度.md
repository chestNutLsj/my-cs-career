# 单处理器调度
## 处理器调度类型

![[Pasted image 20230805170855.png]]

![[Pasted image 20230805171101.png]]

![[Pasted image 20230805171115.png]]
### 长程调度
决定哪个程序可以进入系统中处理，因此控制了系统的并发度。一旦进入，作业或程序就成为进程，并添加到供短程调度程序使用的队列中，等待调度。

### 中程调度
与系统交换功能有关，参看 20、40 两章。

换入取决于管理系统并发度的需求，换出考虑进程的存储需求。

### 短程调度
短程调度程序（dispatcher），执行最为频繁，精确地决定下一次执行哪个进程。

导致当前进程阻塞或抢占当前运行进程的事件发生时，如时钟中断、IO 中断、Syscall、信号等，短程调度程序启动。

## 调度算法
### 短程调度规则
1. **User Oriented, Performance Related**
	1. *Turnaround time* This is the interval of time between the submission of a process and its completion. Includes actual execution time plus time spent waiting for resources, including the processor. This is an appropriate measure for a batch job.
	2. *Response time* For an interactive process, this is the time from the submission of a request until the response begins to be received. Often a process can begin producing some output to the user while continuing to process the request. Thus, this is a better measure than turnaround time from the user’s point of view. The scheduling discipline should attempt to achieve low response time and to maximize the number of interactive users receiving acceptable response time.
	3. *Deadlines* When process completion deadlines can be specified, the scheduling discipline should subordinate other goals to that of maximizing the percentage of deadlines met.
2. **User Oriented, Other**
	1. *Predictability* A given job should run in about the same amount of time and at about the same cost regardless of the load on the system. A wide variation in response time or turnaround time is distracting to users. It may signal a wide swing in system workloads or the need for system tuning to cure instabilities.
3. **System Oriented, Performance Related**
	1. *Throughput* The scheduling policy should attempt to maximize the number of processes completed per unit of time. This is a measure of how much work is being performed. This clearly depends on the average length of a process, but is also influenced by the scheduling policy, which may affect utilization.
	2. *Processor utilization* This is the percentage of time that the processor is busy. For an expensive shared system, this is a significant criterion. In single-user systems and in some other systems, such as real-time systems, this criterion is less important than some of the others.
4. **System Oriented, Other**
	1. *Fairness* In the absence of guidance from the user or other system-supplied guidance, processes should be treated the same, and no process should suffer starvation.
	2. *Enforcing priorities* When processes are assigned priorities, the scheduling policy should favor higher-priority processes.
	3. *Balancing resources* The scheduling policy should keep the resources of the system busy. Processes that will underutilize stressed resources should be favored. This criterion also involves medium-term and long-term scheduling.

1. **以用户为本，注重绩效**
	1. *周转时间* 这是一个进程从提交到完成的时间间隔。包括实际执行时间和等待资源（包括处理器）的时间。这是批处理作业的适当衡量标准。
	2. *响应时间* 对于交互式进程，这是从提交请求到开始收到响应的时间。通常情况下，进程可以在继续处理请求的同时开始向用户提供一些输出。因此，从用户的角度来看，这是一个比周转时间更好的衡量标准。调度规则应努力实现较低的响应时间，并使获得可接受响应时间的交互式用户数量最大化。
	3. *最后期限* 当可以指定流程完成的最后期限时，调度纪律应将其他目标置于最大限度地提高最后期限满足率的目标之下。
2. **以用户为导向，其他**
	1. *可预测性* 无论系统负载如何，给定任务都应在大致相同的时间内以大致相同的成本运行。响应时间或周转时间的巨大差异会分散用户的注意力。它可能预示着系统工作量的大幅波动，或需要对系统进行调整以消除不稳定性。
3. **面向系统，与性能有关**
	1. *吞吐量* 调度策略应尽量增加单位时间内完成的进程数量。这是对正在执行的工作数量的衡量。这显然取决于进程的平均长度，但也受调度策略的影响，后者可能会影响利用率。
	2. *处理器利用率* 这是处理器忙碌时间的百分比。对于昂贵的共享系统来说，这是一个重要的标准。在单用户系统和其他一些系统（如实时系统）中，这一标准不如其他一些标准重要。
4. **面向系统、其他**
	1. *公平性* 在没有用户指导或其他系统提供指导的情况下，进程应受到同等对待，任何进程都不应处于饥饿状态。
	2. *执行优先级* 当进程被指定优先级时，调度策略应有利于优先级较高的进程。
	3. *平衡资源* 调度策略应使系统资源保持繁忙。应优先考虑那些未充分利用紧张资源的进程。这一标准还涉及中期和长期调度。
### 优先级的使用
![[Pasted image 20230813181429.png]]

优先级相关的调度有可能会有饥饿现象，解决办法是动态调整优先级。

### 调度策略
![[Pasted image 20230813181754.png]]
**参数**：
- $w$：目前为止在系统中停留时间；
- $e$：目前为止执行时间；
- $s$：进程需要的总服务时间。

**决策模式**：
1. 非抢占：只要开始执行，就不会被其他进程打断，只能自己申请资源或服务而主动放弃；
2. 抢占：正在运行的进程被 OS 中断，转换为就绪态，在*新进程到达*、*中断发生后将阻塞态进程置为就绪态*、*周期性时间中断时*需要抢占决策；
抢占虽然有较大开销，但避免了进程长期占用 CPU 的情形，有效提高系统响应率。

**实例运行**：
- 周转时间（turnaround time）= 驻留时间 $T_{r}$ = 等待时间+服务时间
- 归一化周转时间 = 周转时间/服务时间，
	- 表示进程的相对延迟情况，
	- 比值越大，服务级别越低，最低为 1.0
	- 通常进程执行时间越长，可容忍的延迟时间越长。

![[Pasted image 20230813183408.png]]

![[Pasted image 20230813183452.png]]

#### First Come, First Serve
![[Pasted image 20230813183742.png]]
==特点==：
- **更适用于长进程**。短进程紧跟在长进程之后，会使归一化周转时间很不协调。
- **更适用于 CPU 密集型进程**，而不是 IO 密集型。例如，
	- 若 CPU 密集型进程在运行，则所有 IO 密集型进程等待，而 IO 队列中的阻塞态进程在等待中可能恢复就绪，但此时不仅无法获得 CPU，IO 设备也变为空闲；
	- 若 IO 密集型进程运行，快速完成 CPU 后等待 IO 返回，此时 CPU 空闲；
	- 因此综合来看，CPU 和 IO 设备的利用率都不高。

#### Round Robin

#### Shortest Process Next

#### Shortest Remaining Time

#### Highest Response Ratio Next

#### Feedback

#### Fair Share 

# 多处理器调度
