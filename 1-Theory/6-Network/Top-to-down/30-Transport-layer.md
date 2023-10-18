## 本章内容

- 理解传输层的工作原理
    - 多路复用/解复用
    - 可靠数据传输(reliable data transfer, RDT)
    - 流量控制
    - 拥塞控制
- 学习Internet的传输层协议
    - UDP：无连接传输
    - TCP：面向连接的可靠传输
    - TCP的拥塞控制

## 3.1 概述和传输层服务

### 传输服务和协议
#### 传输层最重要的两个功能
1. 为运行在不同主机上的应用进程提供**逻辑通信**
	- 两个实体如何通过可能丢失和损坏数据的介质进行可靠通信
	- 看上去是通过 socket api 将数据传给另一个进程，实际上报文需要通过层间接口交给传输层，通过两个传输层之间的相互配合交给另一方对等的应用进程
2. 控制传输层实体的传输速率
	- 避免网络拥塞或从拥塞中恢复

#### 传输协议运行在端系统的过程
![[30-Transport-layer-logical-communication.png]]
- 发送方：将应用层的报文分成报文段，然后传递给网络层。网络层路由器在路由报文时，仅作用于网络层字段，不检查传输层及应用层字段
- 接收方：将报文段重组成报文，然后传递给应用层

- 有多个传输层协议可供应用选择
    - Internet：TCP和UDP

### 传输层 vs. 网络层
- 网络层服务（IP协议）：主机之间的逻辑通信
- 传输层服务：将主机-主机的通信细分为进程间的逻辑通信
    - 依赖于网络层的服务（IP协议）
        - 延时（传输、传播、排队等）、带宽（吞吐量，瓶颈链路）
    - 并对网络层的服务进行增强
        - 数据丢失、顺序混乱、加密
        - 如：IP 向上层提供的服务不可靠，TCP 将不可靠变为可靠：RDT。通过 SSL 将 TCP 由不安全变为安全

有些服务是可以加强的：不可靠 -> 可靠；安全    
但有些服务是不可以被加强的：带宽，延迟

> [! example] 类比：东、西2个家庭的通信
> 
> Ann 家的12个小孩给 Bill 家的12个小孩发信
> - 应用层报文 = 信封上的字符
> - 主机 = 家庭
> - 进程 = 小孩们
> - 传输层协议 = Ann 和 Bill
>     - 为家庭小孩提供复用、解复用服务
>     - Ann 将信件复用（打包）发给邮政服务，Bill 从邮政服务收到后进行解复用（拆包分发），当然反过来也可以
> - 网络层协议 = 邮政服务
>     - 家庭-家庭的邮包传输服务
> 
> 可以从上面看出以下几点：
> 1. 传输层协议只工作在端系统中，对报文在网络核心中如何移动并不作任何规定（全部交给网络层去做）
> 2. Ann 和 Bill 可以替换，比如换成另两个孩子——对应于协议的改变，如 TCP、UDP
> 3. Ann 和 Bill 能提供的服务受制于邮政服务——传输层协议的功能受制于网络层提供的服务


### Internet 传输层协议概述
- 可靠的、保序的传输：TCP (Transmission Control Protocol)
    - 多路复用、解复用
    - 拥塞控制
    - 流量控制
    - 面向连接
- 不可靠、不保序的传输：UDP (User Datagram Protocol)
    - 多路复用、解复用
    - 无连接
    - 没有为尽力而为的 IP 服务添加更多的其它额外服务，UDP 本身就是尽力服务型协议
- 都不提供的服务（依赖于网络层）：
    - 延时保证
    - 带宽保证

## 3.2 多路复用与解复用

### 多路复用与多路分解

- 将网络层提供的主机到主机的交付服务，延伸到为运行在主机上的应用程序提供进程到进程的交付服务。

![[30-Transport-layer-multiplex-demultiplex.png]]
- 回顾：一个进程可以有一个或多个 socket，socket 是网络和进程间传递数据的门户；
- 由图可以见得：接收方主机的运输层实际上并没有直接将数据交付给进程，而是将数据交给了一个 socket ；
- socket 有唯一的标识符，用以区分不同进程和进程的不同功能模块。标识符的格式取决于 TCP 还是 UDP 的 socket。

#### 在发送方主机多路复用    
从源主机的多个套接字接收来自多个进程的报文，根据套接字对应的 IP 地址和端口号等信息对报文段封装以首部信息（该头部信息用于以后的解复用）

#### 在接收方主机多路解复用 
根据报文段的头部信息中的 IP 地址和端口号将接收到的报文段发给正确的套接字（和对应的应用进程）

- 主机收到 IP 数据报
    - 每个数据报有源 IP 地址和目标地址
    - 每个数据报承载一个传输层报文段
    - 每个报文段有一个源端口号和目标端口号（特定应用有著名的端口号）
- 主机联合使用 **IP 地址** 和 **端口号** 将报文段发送给合适的套接字

#### 传输层报文段中源与目的端口字段
Socket 有唯一的标识符，每个报文段有特殊字段来指示该报文来自哪个端口、发向哪个端口，这些特殊字段就是：
![[30-Transport-layer-source-destination-port-fields.png]]
- 源端口号字段 (source port number field)；
- 目的端口号字段 (destination port number field)；
- 其它首部字段留待协议使用：TCP 和 UDP 需要的字段不同；

### 无连接(UDP)多路解复用
#### 创建套接字
服务器端：serverSocket 和 sad 指定的端口号捆绑
```python
serverSocket = socket(PF_INET, SOCK_DGRAM, 0);
bind(serverSocket, &sad, sizeof(sad));
``` 

客户端：没有 Bind，ClientSocket 和 OS 为之分配的某个端口号捆绑（客户端使用什么端口号无所谓，客户端主动找服务器）
```python
ClientSocket = socket(PF_INET, SOCK_DGRAM, 0);
```

- UDP socket 用二元组唯一地标识：(destination IP address, destination port)
- 如果两个不同源 IP 地址/源端口号的数据报，但是有相同的目标 IP 地址和端口号，则被定位到相同的目标 UDP 套接字。
- 当主机收到 UDP 报文段：
	- 检查报文段的目标端口号
	- 用该端口号将报文段定位给套接字

> [! example] 例子：UDP socket 传送报文
> 主机 A 中某进程的源 UDP 端口 19157，发向目的主机 B 的 UDP 端口 46428 的进程：
> ![[30-Transport-layer-UDP-socket.png]]
> - 主机 A 中运输层创建一个运输层报文段，其中包括应用程序数据、源端口号 19157、目的端口号 46428 。
> - 接着传输层将得到的报文段传递到网络层，网络层将该报文段封装到一个 IP 数据报中，并尽力地将数据报交付给接收主机；
> - 如果该报文段到达接收主机 B，接收主机运输层就检查该报文段中的目的端口号 46428，并将该报文段交付给端口号 46428 所标识的 socket。
> 
> ==似乎少了什么？源端口号做什么去了？==
> 源端口号作为“返回地址”，当 B 需要回发一个报文段给 A 时，B 到 A 的报文段中的目的端口号便从 A 到 B 的报文中源端口号字段提取得到。

### 面向连接(TCP)的多路复用
- TCP 套接字：四元组本地标识：(源 IP 地址, 源端口号, 目标 IP 地址, 目标端口号)
- 四元组全部用以将报文段定向（分解）到相应的 TCP socket，
- 与 UDP socket 不同的是，两个具有不同源 IP 地址或源端口号的到达 TCP 报文段，将被定向到两个不同的 TCP socket，除非 TCP 报文段携带了初始创建连接的请求

>[! note] 回顾 TCP 客户端-服务器编程
>1. The TCP server application has a “welcoming socket,” that waits for connection establishment requests from TCP clients (see Figure 2.29) on port number 12000. 
>2. The TCP client creates a socket and sends a connection establishment request segment with the lines:
> ```
>clientSocket = socket (AF_INET, SOCK_STREAM) clientSocket.connect ((serverName, 12000)) 
> ```
>
>3. A connection-establishment request is nothing more than a TCP segment with destination port number 12000 and a special connection-establishment bit set in the TCP header (discussed in Section 3.5). The segment also includes a source port number that was chosen by the client. 
>4. When the host operating system of the computer running the server process receives the incoming connection-request segment with destination port 12000, it locates the server process that is waiting to accept a connection on port number 12000. The server process then creates a new socket: 
> ```
>connectionSocket, addr = serverSocket.accept ()
> ```
>
>5. Also, the transport layer at the server notes the following four values in the connection-request segment: 
>	1) the source port number in the segment, 
>	2) the IP address of the source host, 
>	3) the destination port number in the segment, and 
>	4) its own IP address. 
>	The newly created connection socket is identified by these four values; all subsequently arriving segments whose source port, source IP address, destination port, and destination IP address match these four values will be demultiplexed to this socket.
>
>With the TCP connection now in place, the client and server can now send data to each other.


- 服务器能够在一个 TCP 端口上同时支持多个 TCP socket，每个 socket 由其四元组标识
- 当一个 TCP 报文段到达主机时，所有 4 个字段被用来将报文段定向到相应的 socket。

>[! example] TCP socket 传送报文
>主机 C 向服务器 B 发起两个 HTTP 会话，主机 A 向服务器 B 发起一个 HTTP 会话：
> ![[30-Transport-layer-TCP-socket.png]]
>- 主机 A、B、C 都有各自的 IP 地址，
>- 主机 C 为两个 HTTP 连接分配 26145、7532 两个不同的 socket 端口，
>- 主机 A 分配 socket 时与主机 C 不相干，因此也可以创建 ID 为 26145 的socket

### Web Server 与 TCP
- 高性能 Web Server 通常只使用一个进程，但是为每个新客户连接创建一个具有新连接 socket 的新线程，由线程为客户提供服务；
- 在这个场景下，还是根据4元组决定将报文段内容同一个进程下的不同线程关联，解复用到不同线程（与解复用到不同进程相似）

- Web 服务器对持久连接的 HTTP，在持续期间经由同一个服务器 socket 交换 HTTP 报文
    - 非持久连接的 HTTP，则对每个请求有不同的套接字，这种频繁创建和关闭 socket 会严重影响 Web Server 的性能；

## 3.3 无连接传输：UDP

UDP(User Datagram Protocol `[RFC 768]`)：用户数据报协议
- “no frills,” “bare bones”Internet传输协议
- “尽力而为”的服务，报文段可能丢失也可能送到应用进程的报文段乱序
- 仅提供最低限度的 复用/解复用 服务，以便能通过网络层
- 无连接：
    - UDP 发送端和接收端之间**没有握手**
    - 每个 UDP 报文段都被独立地处理
- UDP 被用于：
    - 流媒体（丢失不敏感，速率敏感、应用可控制传输速率）
    - **DNS**
    - SNMP（简单网络管理协议）
- 在 UDP 上若要实现可靠传输：
    - 在应用层增加可靠性
    - 应用特定的差错恢复


### UDP 的优势
- **适合实时应用**：不需要较高的发送速率，而要求较低的报文段发送延迟，能够容忍部分数据丢失；而 TCP 的拥塞控制在此时会导致实时应用的性能变差；
- **适合时延敏感的应用**：如对 DNS 来说，UDP 不会引入建立连接时延；HTTP 通常是建立在 TCP 上的，但是 HTTP/3.0、Google Chrome 的私有 QUIC 协议，是通过 UDP 来提高速率、在应用层保证可靠性；
- **不必维护链接**：因此可以支持的活动用户数量更多、更适合突发情况；
- **分组首部开销小**：UDP 首部开销仅 8 字节，TCP 则是 20 字节，这对数据较小时优势明显；

### 常用应用的协议
![[30-Transport-layer-applications-protocol.png]]

> [! note] SNMP 为什么选用 UDP？
> SNMP 作为网络管理协议，使用 UDP 则能够在网络处于重压状态下运行，而 TCP 的拥塞控制策略会使数据传输难以实现。

>[!note] 流式多媒体应用越来越青睐 TCP
>1. 在拥塞状态下，UDP 的情况会越来越恶化——如果每个人都启动流式高比特率视频而不使用任何拥塞控制手段的话，会导致路由器中有大量分组溢出，最终只有极少量的 UDP 分组能够成功到达目的地。
>2. 无控制的 UDP 发送方导致的高丢包率，将引起 TCP 发送方大大减少它们的速率，从而挤垮 TCP 回话，这个问题相当严重。

### UDP报文段格式

![[30-Transport-layer-UDP-segment-structure.png]]
- 报文段的头部很小，只有四个部分，每个部分 2 字节
	- 源端口号和目的端口号用于进程交付；
	- 长度字段指示了 UDP 报文段中的字节数：头部+应用数据
	- 校验和用来检查报文段在传输过程中是否出现了差错

### UDP 校验和
校验和：EDC (Error Detection and Correction)，差错检测码
- 目标：检测在被传输报文段中的差错（如比特反转），若出错，这个UDP数据报就会被扔掉（表现为丢失）
- 发送方：
    - 将报文段的内容每16bit 切一段，得到一系列长度为 16 比特的段
    - 校验和：将所有 16 比特的段相加，加法中遇到的所有溢出都加回最低位，最后进行反码运算；
    - 发送方将校验和放在 UDP 报文段的校验和字段
- 接收方：
    - 计算接收到的报文段的校验和：
	    - 将收到的校验和字段与报文段全部的 16 比特的段加起来（包括校验和本身）
	    - 如果结果是全 1 串，没有出错；否则至少有一个错误，==整个报文丢弃==或者==警告地发送给应用程序==（校验和没有纠错能力，只能检错）；


> [! example] Internet 校验和的例子：两个16bit 的整数相加
> 
> 注意：当数字相加时，在最高位的进位要回送到最低位，再加到结果上（即最高位的进位数字与末位相加，重新计算得到和）
>
> suppose that we have the following three 16-bit words:
> ![[30-Transport-layer-checksum-example.png]]
> 
> - Note that this last addition had overflow, which was wrapped around. 
> - The 1s complement is obtained by converting all the 0s to 1s and converting all the 1s to 0s. Thus, the 1s complement of the sum 0100101011000010 is 1011010100111101, which becomes the checksum. 
> - At the receiver, all four 16-bit words are added, including the checksum. If no errors are introduced into the packet, then clearly the sum at the receiver will be 1111111111111111. If one of the bits is a 0, then we know that errors have been introduced into the packet.

>[! note] 端到端原则
>在无法确保每条链路的可靠性、也无法确保中途路由器内存的差错检测时，如果端到端的数据传输要求提供服务检测，那么 UDP 必须在端到端基础上在传输层提供差错检测。（即便校验和是最基本的、只能提供检错能力的差错检测机制）
>
>**端到端原则**：This is an example of the celebrated **end-end principle** in system design `[Saltzer 1984]`, which states that since certain functionality (error detection, in this case) must be implemented on an end-end basis: “functions placed at the lower levels may be redundant or of little value when compared to the cost of providing them at the higher level.”

## 3.4 可靠数据传输的原理

Reliable Data Transfer 在应用层、传输层和数据链路层都很重要，是网络 Top 10问题之一。

信道的不可靠特点（只是“尽力而为”）决定了可靠数据传输协议 (Reliable Data Transfer Protocol) 的复杂性。

### 问题描述

![[30-Transport-layer-rdt.png]]
- RDT 协议的功能：为上层实体提供可靠信道的服务抽象
	- 数据可以通过一条可靠的信道进行传输，在可靠信道中传输数据不会导致其损坏或丢失；
	- 所有数据都是按照发送顺序进行交付；
	- TCP 就是 RDT 类型的协议，UDP 是 UDT 类型的协议；
- 因此，RDT 协议是建立在不可靠的信道、不可靠的底层协议上的，实现起来比较困难、复杂。
- 根据上图 b 的语义，我们规定：
	- 调用 `rdt_send()` 函数，上层可以调用数据传输协议的发送方，将要发送的数据交付给位于接收方的高层；
	- `rdt_send()` 函数会通过 `udt_send()` 调用不可靠的信道传输数据，这意味着数据传输中可能被损坏、丢失，但是为了简便目前不考虑底层信道重排序分组的问题；
	- 接收端在分组从信道到达时，调用 `rdt_rcv()` 接收分组，并对分组进行检查，若无问题，则通过 `deliver_data()` 向高层交付数据。

### 构造可靠数据传输协议
#### 工作安排：
- 渐增式地开发可靠数据传输协议 (rdt) 的发送方和接收方（渐增式：从下层可靠、不丢失开始，一步步去掉假设，使下层变得越来越不可靠，从而完善 rdt 协议）
- 只考虑单向数据传输（半双工）。双向数据传输（全双工）只是形式上复杂，并没有触及 RDT 的核心问题，暂时忽略；
- 控制信息是双向流动的，通过 `rdt` 函数交换控制分组，并且都要通过 `udt_send` 发送给对方。
- 双向的数据传输问题实际上是2个单向数据传输问题的综合（两个过程具有对称性）
- 使用有限状态机 (FSM) 来描述发送方和接收方（有限状态机实际上就是描述协议如何工作的一个形式化的描述方案，比语言更加简洁易懂、便于检查）
	- 状态：在该状态时，下一个状态只由下一个事件唯一确定。
	- 节点之间有个状态变迁的边 (edge)连在一起，代表状态 1 变成状态 2 在变迁的这条有限边上有标注。
	- 标注有分子和分母： $\frac{引起状态变化的事件}{状态变迁时采取的动作}$

下面进行渐进式开发： Rdt1.0 --> Rdt2.0 --> Rdt2.1 --> Rdt2.2 --> Rdt 3.0

#### RDT1.0

Rdt1.0：在可靠信道上的可靠数据传输
- 下层的信道是完全可靠的
    - 没有比特出错
    - 没有分组丢失
- 发送方和接收方的FSM
    - ![[30-Transport-layer-rdt10.png]]
    - 发送方和接收方有各自的 FSM ，每个 FSM 都只有一个状态，状态转移就是从自身通过中间态（发送分组和接收分组的动作）后回到自身
    - ==横线上方是表示引起变迁的事件，下方是事件发生时采取的动作==：
	    - The sending side of `rdt` simply accepts data from the upper layer via the `rdt_send(data)` event, creates a packet containing the data (via the action `make_pkt(data)`) and sends the packet into the channel. In practice, the `rdt_send(data)` event would result from a procedure call (for example, to `rdt_send()`) by the upper-layer application.
	    - On the receiving side, `rdt` receives a packet from the underlying channel via the `rdt_rcv(packet)` event, removes the data from the packet (via the action `extract(packet, data)`) and passes the data up to the upper layer (via the action `deliver_data(data)`). In practice, the `rdt_rcv(packet)` event would result from a procedure call (for example, to `rdt_rcv()`) from the lower-layer protocol.


#### RDT2.0
Rdt2.0：去掉一个假设，变为具有比特差错（如0和1的反转）的信道
- 下层信道可能会出错：
    - 分组中的比特可能会翻转，但仍假定分组顺序不变、分组不会丢失
- 怎样确认报文被接收的情况？
    - **确认(ACK，positive acknowledgement)**：接收方显式地告诉发送方分组已被正确接收（send ACK）
    - **否定确认(NAK, negative acknowledgement)**：接收方显式地告诉发送方分组发生了差错（send NAK）
        - 发送方收到 NAK 后，发送方重传分组（之前发送完之后需要保存一个副本）
- **自动重传请求协议** (Automatic Repear reQuest, ARQ)：指基于重传机制的可靠数据传输协议。ARQ 协议提供三种功能处理比特位差错：
    1) 差错检测：用校验和来检测比特流是否出现差错
    2) 接收方反馈：ACK 与 NAK 反馈分组，这些分组只需要一个头部和一个表示位
    3) 重传：受到 NAK 时，重发分组
- rdt2.0 的 FSM：
    - ![[30-Transport-layer-rdt20-fsm.png]]
    - **发送端有两个状态**：等待上层的调用和数据，或者等待接收方的 ACK/NAK 反馈分组，根据反馈报文的不同进行不同的动作——回到等待上层调用，或者重发上一个分组并继续等待反馈报文；
    - *注意:发送方处于等待 ACK/NAK 反馈时，不能接收上层的调用和数据，仅当接收到 ACK 反馈时才会离开等待反馈报文的状态，因为停下来等待，==rdt2.0 及类似行为的协议被称为停等协议==(stop-and-wait)*；
    - **接收端有一个状态**：分组到达时，根据校验和返回一个 ACK/NAK 反馈报文；
- rdt2.0中的新机制：采用差错控制编码进行差错检测
    - 发送方差错控制编码、缓存
    - 接收方使用编码检错
    - 接收方的反馈：控制报文（ACK，NAK）：接收方->发送方
    - 发送方收到反馈相应的动作（发送新的或者重发老的）

#### RDT2.1
Rdt2.0的致命缺陷！
- 如果 ACK/NAK 出错？
    - 发送方不知道接收方发生了什么事情！（既不是 ACK 也不是 NAK）
    - 发送方如何做？
        - 不顾一切地重传？可能重复
        - 不重传？可能死锁（发送方等 ACK，接收方等重发的分组，尬住了）
    - 需要引入新的机制
        - **序号**
- 处理重复：
    - 发送方在每个分组中加入序号
    - 如果 ACK/NAK 出错，发送方重传当前分组
    - 接收方丢弃（不发给上层）重复分组

- 发送方：
    - 在分组中加入序列号
    - 只需要一位即两个序列号（0，1）就足够了
        - 一次只发送一个未经确认的分组（注：若接收方等待的是1号分组，而传来0 号未出错的分组，则接收方传回 ack，与发送方调成同步）
    - 必须检测 ACK/NAK 本身是否出错（需要 EDC）
    - 状态数变成了两倍
        - 必须记住当前分组的序列号为0还是1
- 接收方：
    - 必须检测接收到的分组是否是重复的
        - 状态会指示希望接收到的分组的序号为 0 还是 1
    - 注意：**接收方并不知道发送方是否正确收到了其最后发送的 ACK/NAK**
        - 发送方不对收到的 ack/nak 给确认，没有所谓的确认的确认；
            - 两军悖论：![[30-Transport-layer-two-army-problem.png]]
        - 接收方发送 ack，如果后面接收方收到的是：
            - 老分组 p0？则 ack 错误
            - 下一个分组？P1，ack 正确

![[30-Transport-layer-two-general-problem.png]]
- rdt2.1 的 FSM：
	- ![[30-Transport-layer-rdt21-fsm.png]]
	- 注意到发送方和接收方的状态数都是之前的 2 倍，因为必须要反应出目前发送方正发送的分组或接收方希望收到的分组的序号是 0 还是 1；
	- 接收方在收到失序或正确的分组时，发送肯定确认 ACK；收到错误的分组时，发送否定确认 NAK；

#### RDT2.2
Rdt2.2：无NAK、只有ACK的协议（NAK free）
- 功能同 rdt2.1，但只使用 ACK（ACK 要编号）：
	- 如果收到错误分组（如分组 0），不发送 NAK0 而是发送上一个正确接收的分组的 ACK1，也能实现同样的效果——
	- 发送方收到上一个分组 1 的两个 ACK（这里称作冗余 duplicate ACK），就知道接收方没有正确接收到分组 1 后的分组 0
- 接收方对**最后正确接收的分组发 ACK**，以替代 NAK（==对当前分组的反向确认可由对前一项分组的正向确认代表==，如用 ACK0 代表 NAK1、用 ACK1 代表 NAK0 等等）
    - 接收方必须显式地包含被正确接收分组的序号
- 当收到重复的 ACK（如：再次收到 ACK0）时，发送方与收到 NAK1 采取相同的动作：重传当前分组 0
- NAK free 的意义：
    - 为后面的一次发送多个数据单位做一个准备
    - 一次能够发送多个
    - 每一个的应答都有：ACK，NAK；麻烦
    - 使用对前一个数据单位的ACK，代替本数据单位的nak
    - ==确认信息减少一半，协议处理简单==

- rdt2.2 的 FSM：
	- ![[30-Transport-layer-rdt22-fsm.png]]

#### RDT3.0
Rdt3.0：具有比特差错和分组丢失的信道
- 新的假设：下层信道可能会丢失分组（数据或 ACK 都可能丢失）
    - 会死锁（发送方等待确认，接收方等待分组）
    - Rdt2.2 的机制外，还需增加一种机制处理这种状况：
        - 检验和
        - 序列号
        - ACK
        - 重传
        - ？  ———————— 定时！
- 方法：发送方等待 ACK 一段合理的时间（链路层的 timeout 时间是确定的，传输层 timeout 时间是适应式的（需要**动态地计算**））
    - 发送端**超时重传**：如果到时没有收到ACK->重传
    - 问题：如果分组（或ACK）只是被延迟了：
        - 重传将会导致数据重复（冗余分组），但利用序列号已经可以处理这个问题
        - 接收方必须指明被正确接收的序列号
    - 需要一个倒计数定时器 countdown timer

发送方的步骤：
1) 每次发送一个分组（包括第一次分组和重传分组），都启动一个定时器
2) 响应定时器中断，采取适当的动作——重传
3) 收到 ACK 时，及时终止定时器

- rdt3.0 的 FSM：
	- ![[30-Transport-layer-rdt30-fsm.png]]
	- The sender side of protocol rdt3.0 differs from the sender side of protocol 2.2 in that timeouts have been added. We have seen that the introduction of timeouts adds the possibility of duplicate packets into the sender-to-receiver data stream. However, the receiver in protocol rdt.2.2 can already handle duplicate packets. (Receiver-side duplicates in rdt 2.2 would arise if the receiver sent an ACK that was lost, and the sender then retransmitted the old data). ==Hence the receiver in protocol rdt2.2 will also work as the receiver in protocol rdt 3.0==.

- rdt3.0 的运行情况：
	- ![[30-Transport-layer-rdt30-work-flow.png]]
	- 在图 d 中可见：过早超时（延迟的 ACK）也能够正常工作；但是效率较低，一半的分组和确认是重复的；因此，设置一个**合理的超时时间**也是比较重要的

Rdt3.0的性能
- rdt3.0 停等协议可以工作，但链路容量比较大的情况下（分组全部放完时，分组的第一个比特离接收方还很远），性能很差
    - 链路容量比较大，一次发一个 PDU 的不能够充分利用链路的传输能力（信道明明可容纳很多很多包，每次却只有一个包处于信道中，信道利用率极低）
    - 改进办法：[[#流水线 RDT 协议|使用流水线]]！

> [! example] 例：rdt3.0 的低效之处
> $1Gbps$ 的链路， $15ms$ 端-端传播延时（ $RTT = 30ms$ ），分组大小为 $1kB = 1000Bytes = 8000bits$ ：   
> $$T_{transmit} = \frac{L(分组长度, 比特)}{R(传输速率, bps)} = \frac{8kb/pkt}{10^9 b/sec} = 8\mu{s}$$     
> $$U_{sender} = \frac{L/R}{RTT+L/R} = \frac{{0.008}}{30.008} = 0.00027$$
> - $U_{sender}$ ：利用率 – 忙于发送的时间比例
> - 每 $30ms$ 发送 $1KB$ 的分组 --> $270kbps=33.75kB/s$ 的吞吐量（在 $1Gbps$ 链路上）
> - 瓶颈在于：网络协议限制了物理资源的利用！

#### 经验
在构造 rdt1.0 -> rdt3.0 的过程中，我们学习到，为了在不可靠通道中能够提供可靠数据传输，需要满足以下**五个条件**：
1. 检错、纠错机制——校验和
2. 序号——保证顺序、避免丢失和重复
3. 定时器——避免丢失
4. ACK 和 NAK——表明发送是否成功
5. 重传——万能手段，可以恢复失败的发送

### 流水线 RDT 协议
#### 思路——提高 rdt3.0 的信道利用率

如何提高链路利用率？流水线(pipeline)

![[30-Transport-layer-pipeline-rdt.png]]
- 发送报文，不要停等，无需逐个确认！

![[30-Transport-layer-pipeline-rdt-n.png]]
- 增加 $n$ （如这里从 $n=1$ 变为 $n=3$ ），能提高链路利用率，差不多就是提升了 3 倍；
- 但当达到某个 $n$ ，其 $u=100\%$ 时，无法再通过增加 $n$ ，提高利用率
- 瓶颈由协议转移到 --> 链路带宽（此时可将 $1Gbps$ 链路升级为 $10Gbps$ 链路）

流水线协议（流水线：允许发送方在未得到对方确认的情况下一次发送多个分组）
- ==必须增加序号的范围==：用多个 bit 表示分组的序号（若分组的序号用 $N$ 个比特表示，则整个分组的空间占用是 $2^N$ ）
- 在发送方/接收方要有缓冲区
    - 发送方缓冲：未得到确认，可能需要重传；
    - 接收方缓冲：
	    - 上层用户取用数据的速率 $\neq$ 接收到的数据速率，需要缓冲来对抗速率的差异性；
	    - 接收到的数据可能乱序，排序交付（可靠）
- 两种通用的流水线协议：**回退N步(GBN)** 和 **选择重传(SR)**

#### 滑动窗口

为讲解 GBN 和 SR 协议的差别，先引入**滑动窗口(sliding window)**：

| sending window | receiving window | protocol form |
|:--------------:|:----------------:|:-------------:|
|      = 1       |       = 1        |   stop-wait   |
|      > 1       |       = 1        |      GBN      |
|      > 1       |       > 1        |      SR       |

其中 sw(sending window) > 1 时就是流水线协议。

![[30-Transport-layer-sliding-window-protocol.png]]

几个概念：
- **发送缓冲区**
    - 形式：内存中的一个区域，落入缓冲区的分组可以进行检错重发、超时重发
    - 功能：==用于存放已发送，但是没有得到确认的分组==
    - 必要性：需要重发时可用
- 发送缓冲区的大小：一次最多可以发送多少个未经确认的分组
    - 停止等待协议=1
    - 流水线协议>1，合理的值，不能很大，避免链路拥塞
- 发送缓冲区中的分组
    - 未发送的：落入发送缓冲区的分组，可以连续发送出去；
    - 已经发送出去的、等待对方确认的分组：发送缓冲区的分组只有得到确认才能删除

- **发送窗口**：发送缓冲区内容的一个子集
    - ==存放那些已发送但是未经确认分组==的序号构成的空间
    - 发送窗口的最大值 <= 发送缓冲区的值
    - 一开始：没有发送任何一个分组
        - 后沿 = 前沿
        - 之间为发送窗口的尺寸 = 0
    - 发送窗口的移动——前沿移动
        - 每发送一个分组，前沿前移一个单位
        - 发送窗口前沿移动的极限：（前沿和后沿的距离）不能够超过发送缓冲区
    - 发送窗口的移动——后沿移动
        - 条件：收到老分组的确认
        - 结果：发送窗口缩小，等待接下来的 ACK 
        - 移动的极限：不能够超过前沿
    - 发送窗口滑动过程——相对表示方法（只是为了方便理解）
        - 采用相对移动方式表示，分组不动，窗口向前滑动（实际上真正的滑动过程为窗口不动，分组向前滑动）
        - 可缓冲范围移动，代表一段可以发送的权力

- **接收窗口**(receiving window)：
    - ==接收窗口用于控制哪些分组可以接收==；
        - 只有收到的分组序号**落入接收窗口内才允许接收**
        - 若序号在接收窗口之外，则丢弃；
    - 接收窗口尺寸 $W_{rcv}=1$，则只能顺序接收；
        - 例如：$W_{rcv}＝1$，在 0 的位置；只有 0 号分组可以接收；向前滑动一个，罩在 1 的位置，此时如果来了第 2 号分组，则第 2 号分组为乱序分组，则丢弃，并给出确认——对顺序到来的最高序号的分组给确认——发送 ACK0
    - 接收窗口尺寸 $W_{rcv}>1$，则可以乱序接收（但提交给上层的分组，要按序）
        - 例如：$W_{rcv}=4>1$ 时，此时到来的分组是乱序但是落在接收缓冲区的范围之内，则也进行接收并发出确认，==直到该分组之前的分组都接收到了，接收窗口才进行滑动==，如下图中收到 1，2 分组先不滑动，收到 0 分组后，2 分组前的分组全部收到，此时进行滑动到 3 号分组开始；若第一次接收到的是 0 号分组，则接收缓冲区立即向后滑动到 1 开始）：
        - ![[30-Transport-layer-receiving-window.png]]
    - 接收窗口的滑动和发送确认
        - 滑动：
            - 低序号的分组到来，接收窗口移动；
            - 高序号分组乱序到，缓存但不交付（因为要实现 rdt，不允许失序），不滑动
        - 发送确认：
            - ==接收窗口尺寸=1：发送连续收到的最大的分组确认==（累计确认：这之前的分组都正确收到了）
            - ==接收窗口尺寸>1：收到分组，发送那个分组的确认==（单独确认：只代表正确收到了这个分组）

#### 发送窗口-接收窗口的互动
- 正常情况下的窗口互动
    - 发送窗口
        - 有新的分组落入发送缓冲区范围，发送->前沿滑动
        - 来了老的低序号分组的确认->后沿向前滑动->新的分组可以落入发送缓冲区的范围
    - 接收窗口
        - 收到分组，落入到接收窗口范围内，接收
        - 是低序号，发送确认给对方
    - 发送端上面来了分组->发送窗口滑动->接收窗口滑动->发确认
- 异常情况下 GBN(wr=1)的窗口互动
    - 发送窗口
        - 新分组落入发送缓冲区范围，发送->前沿滑动
        - 超时重发机制让发送端将发送窗口中的所有分组发送出去
        - 来了老分组的重复确认->后沿不向前滑动->新的分组无法落入发送缓冲区的范围（此时如果发送缓冲区有新的分组可以发送）
    - 接收窗口
        - 收到乱序分组，没有落入到接收窗口范围内，抛弃
        - （重复）发送老分组的确认，累计确认
- 异常情况下SR(wr>1)的窗口互动
    - 发送窗口
        - 新分组落入发送缓冲区范围，发送->前沿滑动
        - 超时重发机制让发送端将超时的分组重新发送出去（不像GBN需要将发送窗口中的所有分组全部重发）
        - 来了乱序分组的确认->后沿不向前滑动->新的分组无法落入发送缓冲区的范围（此时如果发送缓冲区有新的分组可以发送）
    - 接收窗口
        - 收到乱序分组，落入到接收窗口范围内，接收
        - 发送该分组的确认，单独确认

#### Go-Back-N
- GBN 协议的窗口：
	-  ![[30-Transport-layer-GBN-sender-seq.png]]
	- 基序号 `base`：最早未确认的分组的序号
	- 下一个序号 `nextseqnum`：下一个待发分组的序号
	- `[0,base)`：已发送并确认的分组
	- `[base,nextseqnum)`：已发送待确认的分组
	- `[nextseqnum,base+N)`：窗口内待发送的分组。N 是窗口长度
		- 为什么要限制窗口长度 N？——流量控制
	- `[base+N,buffer_max)`：缓存区中、窗口外的不可发送分组。

- 序号：
	- 由于 GBN 协议窗口大小为 N，因此需要 $\log_{2}N$ 个比特作序号标记分组，该序号存储在分组首部的固定长度字段中；
	- 序号计算使用模 $2^{\log_{2}N}$ 运算，即序号空间中 $2^{\log_{2}N}-1$ 的下一位是 0；

- GBM 的 FSM：
	- ![[30-Transport-layer-GBN-FSM.png]]
	- GBN 发送方必须响应三类事件：
		1) 上层调用：调用 `rdt_send()` 前，发送方检查发送窗口是否已满，未满则可发送，已满则缓存数据、或使用同步机制告知上层（生产者消费者模型）
		2) 收到一个 ACK：GBN 使用累积确认的方式，表明 ACK-n 之前的分组都已收到；
		3) 超时事件：一旦出现超时，重传所有已发送但未确认的分组。上图中仅使用一个定时器，作为最早已发送但未确认的分组的定时器，收到后续 ACK 却仍有未确认分组，定时器就重置，否则停止；
	- GBN 接收方动作：
		- 序号为 n 的分组被接收，且之前的分组业已接收并交付给上层，则发送 ACK-n 并交付；
		- 其它情况则直接丢弃分组 n，并为最近按序接收的分组重传对应 ACK；
	- 优点：接收方不必缓存任何失序分组，简单实现；
	- 缺点：丢弃正确的分组导致低效

- GBK 的运行情况：
	- ![[30-Transport-layer-GBN-operation.png]]
	- 由于窗口大小的限制，发送方发送数据包 0 至 3 后，必须等待其中一个或多个数据包得到确认后才能继续发送。当收到每个连续的 ACK（例如 ACK0 和 ACK1）时，窗口向前滑动，发送方可以发送一个新数据包（分别为 pkt4 和 pkt5）。在接收端，数据包 2 丢失，因此数据包 3、4 和 5 被认为不符合顺序而丢弃。
	- 正常情况：
		- ![[30-Transport-layer-GBN.gif]]
	- 发送中丢包：
		- ![[30-Transport-layer-GBN-send-loss.gif]]
		- 当发送过程中的 2 号包发生丢失时，发送方没有收到接收方的 ACK2，于是后面发送的 ACK3, ACK4 全部变成了 ACK1，代表接收方因为丢失了分组 2，所以分组 3 和分组 4 都被丢弃。所以全部返回 ACK1，经过一段时间后，定时器确认超时没有收到 ACK3, ACK4，所以发送方将重新发送。也代表接收方首先只收到了分组 1 及之前的包。
	- 接收中丢包：
		- ![[30-Transport-layer-GBN-receive-loss.gif]]
		- 在此例中，我们可以看到，当接收方接收完消息以后，返回给发送方 ACK0, ACK1, ACK2, ACK3, ACK4，我们假设 ACK2 发生了丢失。根据上一例我们可以知道，如果接收方没有收到分组 2，则后面返回的都是 ACK1，因为本次返回的为 ACK3, ACK4，所以发送方可以判断接收方已经接收到消息，不再进行重复发送。

#### Selective Repeat

GBN 的缺点：在信道差错率较高时，一旦一个分组丢失将引起大量分组重传，最终导致信道中充斥着重传的分组，利用率极低。

- SR 协议的窗口：
	- ![[30-Transport-layer-SR-sender-windows.png]]
	- 发送方仅重传它怀疑未接收到的分组，从而避免不必要的重传；
	- 发送方窗口中可以离散地分布着发送且确认的分组，这一点与 GBN 不同；
	- 接收方确认每个正确接收的分组，无论其是否按序，失序的分组将被缓存直到所有丢失分组都被接收到为止，此时再将一批分组交付给上层应用；
	- ![[30-Transport-layer-SR-sender-receivers-actions.png]]
	- 值得注意的是，在图 3.25 的步骤 2 中，接收方会**重新确认**（而不是忽略）已收到的序列号低于当前窗口基数的数据包。以图 3.23 中的发送方和接收方序列号空间为例，如果没有从接收方传播到发送方的数据包 send_base 的 ACK，发送方最终将重新发送数据包 send_base，尽管接收方已经收到了该数据包。如果接收方不确认这个数据包，发送方的窗口就永远不会向前移动！
	- 发送方和接收方对哪些数据包已正确接收，哪些未正确接收的看法并不总是一致的。对于 SR 协议来说，这意味着发送方和接收方的窗口可能不同，这引入了**同步问题**。

>[! important] SR 协议的发送方和接收方窗口同步问题
> ![[30-Transport-layer-SR-sync.png]]
>The lack of synchronization between sender and receiver windows has important consequences when we are faced with the reality of a finite range of sequence numbers. Consider what could happen, for example, with a finite range of four packet sequence numbers, 0, 1, 2, 3, and a window size of three. 
>
>Suppose packets 0 through 2 are transmitted and correctly received and acknowledged at the receiver. At this point, the receiver’s window is over the fourth, fifth, and sixth packets, which have sequence numbers 3, 0, and 1, respectively. Now consider two scenarios. 
>- In the first scenario, shown in Figure 3.27 (a), the ACKs for the first three packets are lost and the sender retransmits these packets. The receiver thus next receives a packet with sequence number 0—a copy of the first packet sent. 
>- In the second scenario, shown in Figure 3.27 (b), the ACKs for the first three packets are all delivered correctly. The sender thus moves its window forward and sends the fourth, fifth, and sixth packets, with sequence numbers 3, 0, and 1, respectively. The packet with sequence number 3 is lost, but the packet with sequence number 0 arrives—a packet containing new data. 
>
>Now consider the receiver’s viewpoint in Figure 3.27, which has a figurative curtain between the sender and the receiver, since the receiver cannot “see” the actions taken by the sender. All the receiver observes is the sequence of messages it receives from the channel and sends into the channel. As far as it is concerned, the two scenarios in Figure 3.27 are identical. ==**There is no way of distinguishing the retransmission of the first packet from an original transmission of the fifth packet**==. 
>
>Clearly, a window size that is 1 less than the size of the sequence number space won’t work. But how small must the window size be? A problem at the end of the chapter asks you to show that the ==**window size must be less than or equal to half the size of the sequence number space for SR protocols**==.
>> 窗口大小必须小于等于序号空间大小的一半。

- SR 的运行情况：
	- ![[30-Transport-layer-SR-operation.png]]
	- 分组 2 丢失，但是收到分组 3、4、5 时，接收方会缓存它们，直到分组 2 到达再一起交付；
	- 正常情况：
		- ![[30-Transport-layer-SR.gif]]
	- 发送中丢包：
		- ![[30-Transport-layer-SR-send-loss.gif]]
		- 我们同样将分组 2 丢失，但可以看到与 GNB 不同的是，接收方在没有收到分组 2 的情况下，依然返回了 ACK3, ACK4。此时窗口已经由之前的 0-4 变成了 2-6。当所以 ACK1 返回以后，分组 5，分组 6 就已经可以发送。然后在接收方，分组 3456 都已经被缓存，等待分组 2 的计时器超时后，分组 2 将重新发送，然后在接收方的分组 23456 全部变为接收 received 状态。
	- 接收中丢包：
		- ![[30-Transport-layer-SR-receive-loss.gif]]
		- 我们同样将ACK2丢弃，此时发送方的分组已经由0-4到了2-6,在最后2-6的窗口中，分组2会因为ACK2被丢失然后在计时器计时后重新发送一次。==可以看到如果在接收过程中有丢失发生，选择重传SR的效率是不如回退N步GBN的。==

#### GBN协议和SR协议的异同
- 相同之处
    - 发送窗口>1
    - 一次能够可发送多个未经确认的分组
- 不同之处
    - GBN：接收窗口尺寸=1
        - 接收端：只能顺序接收（对顺序接收的最高序号的分组进行确认-累计确认）
            - 对乱序的分组：
                - 丢弃（不缓存）
                - 在接收方不被缓存！
        - 发送端：从表现来看，一旦一个分组没有发成功，如：0,1,2,3,4，假如1未成功，234都发送出去了，要返回1再发送；GB1
            - 只发送ACK：对顺序接收的最高序号的分组
                - 可能会产生重复的ACK
                - 只需记住expectedseqnum；接收窗口=1
                    - 只一个变量就可表示接收窗口
    - SR：接收窗口尺寸>1
        - 接收端：可以乱序接收
        - 发送端：发送0,1,2,3,4，一旦1未成功，2,3,4,已发送，无需重发，选择性发送1

#### 对比 GBN 和 SR 

|      |                  GBN                   |                    SR                    |
|:----:|:--------------------------------------:|:----------------------------------------:|
| 优点 | 简单，所需资源少（接收方一个缓存单元） |          出错时，重传一个代价小          |
| 缺点 |        一旦出错，回退N步代价大         | 复杂，所需要资源多（接收方多个缓存单元） |
- 适用范围
- 出错率低：比较适合GBN，出错非常罕见，没有必要用复杂的SR，为罕见的事件做日常的准备和复杂处理
- 链路容量大（延迟大、带宽大）：比较适合SR而不是GBN，一点出错代价太大

窗口的最大尺寸（若整个的序号空间是 $2^n$ ）
- GBN： $2^n-1$ ，如 $n=2$ 时 $GBN=3$
- SR： $2^{n-1}$ ，如 $n=2$ 时 $SR=2$
    - SR的例子：
        - 接收方看不到二者的区别！
        - 将重复数据误认为新数据(a)

## 3.5 面向连接的传输：TCP

TCP relies on many of the underlying principles discussed in the previous section, including error detection, retransmissions, cumulative acknowledgments, timers, and header fields for sequence and acknowledgment numbers. TCP is defined in RFC 793, RFC 1122, RFC 2018, RFC 5681, and RFC 7323.

### TCP 连接的特点与建立

- **Connection-oriented**: 面向连接
	- TCP is said to be connection-oriented because ==before one application process can begin to send data to another, the two processes must first “handshake” with each other==—that is, they must send some preliminary segments to each other to establish the parameters of the ensuing data transfer. 
	- As part of TCP connection establishment, both sides of the connection will initialize many TCP state variables associated with the TCP connection.

- **Logical connection**: 逻辑连接而非物理连接
	- The TCP “connection” is not an end-to-end TDM or FDM circuit as in a circuit switched network. Instead, the “connection” is a ==logical one, with common state residing only in the TCPs in the two communicating end systems==. 
	- Recall that because the ==TCP protocol runs only in the end systems== and not in the intermediate network elements (routers and link-layer switches), the intermediate network elements do not maintain TCP connection state. In fact, the intermediate routers are completely oblivious to TCP connections; they see datagrams, not connections.

- **Full-duplex service**: 全双工的
	- A TCP connection provides a full-duplex service: If there is a TCP connection between Process A on one host and Process B on another host, then application-layer data can flow from Process A to Process B at the same time as application-layer data flows from Process B to Process A.

- **Point-to-point**: 点到点的
	- A TCP connection is also always point-to-point, that is, between a single sender and a single receiver. 
	- So-called “multicasting” —the transfer of data from one sender to many receivers in a single send operation—is not possible with TCP. With TCP, two hosts are company and three are a crowd!

- 如何建立 TCP 连接？——三次握手
	- Suppose a process running in one host wants to initiate a connection with another process in another host. Recall that ==the process that is initiating the connection is called the client process==, while the other process is called the server process. 
	- The client application process first informs the client transport layer that it wants to establish a connection to a process in the server. By issuing the command `clientSocket.connect((serverName,serverPort))` in Python, TCP in the client then proceeds to establish a TCP connection with TCP in the server. 
	- ==Three-way handshake==: The client first sends a special TCP segment; the server responds with a second special TCP segment; and finally the client responds again with a third special segment. 
		- The first two segments carry no payload (不承载有效载荷), that is, no application-layer data; 
		- the third of these segments may carry a payload. 
		- Because three segments are sent between the two hosts, this connection-establishment procedure is often referred to as a three-way handshake.

- Send data: After connection established
	- ![[30-Transport-layer-TCP-send-receive-buffers.png]]
	- The client process passes a stream of data through the socket. Once the data is put into the socket, the data is in the hands of TCP running in the client.
	- As shown in Figure 3.28, ==TCP directs this data to the connection’s **send buffer==**, which is one of the buffers that is set aside during the initial three-way handshake. From time to time, TCP will grab chunks of data from the send buffer and pass the data to the network layer. Interestingly, the TCP specification `[RFC 793]` is not specifying when TCP should actually send buffered data, stating that TCP should “send that data in segments at its own convenience.” (TCP 应当在它方便的时候以报文段的形式发送数据)
	- The maximum amount of data that can be grabbed and placed in a segment is limited by the **maximum segment size** (MSS). The MSS is typically set by first determining the length of the largest link-layer frame that can be sent by the local sending host (the so-called maximum transmission unit, MTU), and then ==setting the MSS to ensure that a TCP segment (when encapsulated in an IP datagram) plus the TCP/IP header length (typically 40 bytes) will fit into a single link-layer frame==.(MSS 的设置需要保证： TCP 报文段+TCP/IP 首部长度≤链路层帧)
		- Both Ethernet and PPP link-layer protocols have an MTU of 1,500 bytes. Thus, a typical value of MSS is 1460 bytes. 
		- Note that the ==MSS is the maximum amount of application-layer data in the segment, not the maximum size of the TCP segment including headers==.
		- When TCP sends a large file, such as an image as part of a Web page, it typically ==breaks the file into chunks of size MSS== (except for the last chunk, which will often be less than the MSS). Interactive applications, however, often transmit data chunks that are smaller than the MSS; for example, with remote login applications such as ==Telnet and ssh, the data field in the TCP segment is often only one byte==. Because the TCP header is typically 20 bytes (12 bytes more than the UDP header), segments sent by Telnet and ssh may be only 21 bytes in length.

- TCP connection composition:
	- A TCP connection consists of buffers, variables, and a socket connection to a process in one host, and another set of buffers, variables, and a socket connection to a process in another host.
	- As mentioned earlier, ==no buffers or variables are allocated to the connection in the network elements== (routers, switches, and repeaters) between the hosts.

- 可靠的、按顺序的字节流：
    - 没有报文边界，报文界限靠应用进程自己区分
- 管道化（流水线）：
    - TCP 拥塞控制和流量控制设置窗口大小
- 有流量控制：
    - 发送方不会淹没接收方

### 段结构

![[30-Transport-layer-TCP-segment-structure.png]]

注：这里第二行的“序号”不是前面讲的PDU的序号（不是分组号），而是字节的序号：body部分的第一个字节在整个字节流中的偏移量offset（第 $i$ 个MSS的第一个字节在字节流中的位置，初始的序号称为 $X$ ， $X$ 在建立连接时两个进程商量好，第 $n$ 个的序号为 $X+n*MSS$ ）

TCP序号，确认号
- 序号(sequence number, 简写seq)：
    - 报文段首字节的在字节流的编号
- 确认号(acknowledgement number, 简写ack)：
    - 期望从另一方收到的下一个字节的序号
    - 累积确认（如假设ack=555，则表示接收方已经收到了554及之前的所有字节）
- Q：接收方如何处理乱序的报文段？ 没有规定（可以缓存，也可以抛弃，取决于实现者自己）

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726074323701.png" />

> 例：Host A - Host B （双向数据传递）
> 
> 1. A(User types 'C') --> B(host ACKs receipt of 'C', echoes back 'C'): Seq = $42$ , ACK = $79$ , data = 'C';
> 2. B(host ACKs receipt of 'C', echoes back 'C') --> A(host ACKs receipt of echoed 'C'): Seq = $79$ , ACK = $43$ , data = 'C';
> 3. A(host ACKs receipt of echoed 'C') --> B: Seq = $43$ , ACK = $80$ , data = ... 
> 4. ...

### TCP往返延时(RTT)和超时
- Q：怎样设置TCP超时定时器？
    - 比RTT要长
        - 但RTT是变化的
    - 太短：太早超时
        - 不必要的重传
    - 太长：对报文段丢失反应太慢，消极
- Q：怎样估计RTT？
    - SampleRTT：测量从报文段发出到收到确认的时间，得到往返延时
        - 如果有重传，忽略此次测量
    - SampleRTT会变化，因此估计的RTT应该比较平滑
        - 对几个最近的测量值求平均，而不是仅用当前的SampleRTT
- RTT不是一个固定的值，而是一个适应式的测量。由于每个分组经历不一样，严重依赖于网络状况，则SampleRTT变化非常大，用SampleRTT建立超时定时器不合理，需要用SampleRTT的平均值EstimatedRTT（滤波算法）
    - $[EstimatedRTT] = (1-\alpha) * [(previous)EstimatedRTT] + \alpha * SampleRTT$
    - 指数加权移动平均
    - 过去样本的影响呈指数衰减
    - 推荐值： $\alpha = 0.125$
    
    <img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726075613747.png" style="zoom:80%"/>

- 设置超时
    - EstimtedRTT + 安全边界时间(safety margin)
    - EstimatedRTT变化大（方差大）
        - 较大的安全边界时间
    - SampleRTT会偏离EstimatedRTT多远：
        - $[DevRTT] = (1-\alpha) * [(previous)DevRTT] + \alpha * |SampleRTT-EstimatedRTT|$
        - 推荐值： $\alpha = 0.25$
- 超时时间间隔设置为：
    - $TimeoutInterval = EstimatedRTT + 4*DevRTT$

### 可靠数据传输

TCP：可靠数据传输
- TCP在IP不可靠服务的基础上建立了rdt
    - 管道化的报文段
        - GBN or SR
    - 累积确认，期望从另一方收到的下一个字节的序号，对顺序到来的最后一个字节给予确认（像GBN）
    - 单个重传定时器（像GBN），只和最老的段相关联，一旦超时就将最老的段重发一遍（像SR）
    - 是否可以接受乱序的，没有规范
- 通过以下事件触发重传
    - 超时（只重发那个最早的未确认段：SR）
    - 重复的确认（又收到3个冗余确认）（又叫快速重传）
        - 例子：收到了ACK50，之后又收到3个ACK50
- 首先考虑简化的TCP发送方：
    - 忽略重复的确认
    - 忽略流量控制和拥塞控制

TCP发送方事件：
- 从应用层接收数据：
    - 用nextseq创建报文段
    - 序号nextseq为报文段首字节的字节流编号
    - 如果还没有运行，启动定时器
        - 定时器与最早未确认的报文段关联
        - 过期间隔：TimeOutInterval
- 超时：
    - 重传 后沿最老的报文段
    - 重新启动定时器
- 收到确认：
    - 如果是对尚未确认的报文段确认
        - 更新已被确认的报文序号
        - 如果当前还有未被确认的报文段，重新启动定时器

TCP：重传

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726081314233.png" height=300 /><img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726082045415.png" height=300/>

产生TCP ACK的建议 [RFC 1122, RFC 2581]
| 接收方的事件 | TCP接收方动作 |
| --- | --- |
| 所期望序号的报文段按序到达。所有在期望序号之前的数据都已经被确认 | 延迟的ACK（提高效率，少发一个ACK）。对另一个按序报文段的到达最多等待500ms。如果下一个报文段在这个时间间隔内没有到达，则发送一个ACK。|
| 有期望序号的报文段到达。另一个按序报文段等待发送ACK | 立即发送单个累积ACK，以确认两个按序报文段。|
| 比期望序号大的报文段乱序到达。检测出数据流中的间隔 | 立即发送重复的ACK，指明下一个期待字节的序号 |
| 能部分或完全填充接收数据间隔的报文段到达 | 若该报文段起始于间隔(gap)的低端，则立即发送ACK（给确认并反映下一段的需求）。 |

快速重传（3个冗余ACK触发的重发）

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726083307463.png" style="zoom:80%"/>

- 超时周期往往太长：
    - 在重传丢失报文段之前的延时太长
- 通过重复的ACK来检测报文段丢失
    - 发送方通常连续发送大量报文段
    - 如果报文段丢失，通常会引起多个重复的ACK
- 如果发送方收到同一数据的3个冗余ACK，重传最小序号的段；
    - 快速重传：在定时器过时之前重发报文段
    - 它假设跟在被确认的数据后面的数据丢失了
        - 第一个ACK是正常的；
        - 收到第二个该段的ACK，表示接收方收到一个该段后的乱序段；
        - 收到第3，4个该段的ack，表示接收方收到该段之后的2、3个乱序段，有非常大的可能性为段丢失了

三重ACK接收后的快速重传

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726083418896.png" style="zoom:80%" />

### 流量控制

目的：接收方控制发送方，不让发送方发送的太多、太快，超过了接收方的处理能力，以至于让接收方的接收缓冲区溢出

TCP流量控制
- 接收方在其向发送方的TCP段头部的rwnd字段“通告”其空闲buffer大小
    - RcvBuffer大小通过socket选项设置（典型默认大小为4096字节）
    - 很多操作系统自动调整RcvBuffer
- 发送方限制未确认(“in-flight”)字节的个数小于等于接收方发送过来的 rwnd 值
- 保证接收方不会被淹没
- 假设TCP接收方丢弃乱序的报文段，则缓存中的可用的空间：
  $RcvWindow = RcvBuffer - [LastByteRcvd - LastByteRead]$
  
  <img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726115906902.png" style="zoom:60%"/>

### 连接管理

连接
- 两个应用进程建立起的TCP连接
- 连接的本质：
    - 双方知道要和对方通信
    - 为这次通信建立好缓冲区，准备好资源
    - 一些控制变量需要做置位，两者需要互相告知自己的初始序号，初始化的RcvBuffer

在正式交换数据之前，发送方和接收方握手建立通信关系：
- 同意建立连接（每一方都知道对方愿意建立连接）
- 同意连接参数

同意建立连接
- Q：在网络中，2次握手建立连接总是可行吗？不行
    - 变化的延迟（连接请求的段没有丢，但可能超时）
    - 由于丢失造成的重传（e.g. req_conn(x)）
    - 报文乱序
    - 相互看不到对方
    - 2次握手的失败场景：
      
      <img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726121120744.png"/>

解决方案：TCP **3次握手**（变化的初始序号+双方确认对方的序号）
- 第一次：client-->server：客户端一方的初始序号 $x$
- 第二次：server-->client：发送确认 $ACKnum=x+1$ 和服务器一方的初始序号 $y$ （捎带）
- 第三次：client-->server：发送确认 $ACKnum=y+1$ 和客户端data（捎带）

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726121321781.png" />

3次握手解决：半连接和接收老数据问题

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726121605926.png" />

1. 二次握手：可能发送半连接（只在服务器维护了连接）；三次握手：客户端在第三次握手拒绝连接请求 服务器二次握手后的连接请求
2. 二次握手：老的数据被当成新的数据接收了；三次握手：未建立连接（无半连接），故将发来的数据丢掉（扔掉：连接不存在，没建立起来；连接的序号不在当前连接的范围之内）
3. 若一个数据滞留时间足够长导致在TCP第二次连接（两个三次握手后）到来，这个数据包大概率也会被丢弃，因为初始序号seq不一样，而seq又与时间有关（时钟周期的第k位）

TCP：关闭连接（连接释放）：**4次挥手**
- 客户端，服务器分别关闭它自己这一侧的连接 
  - 发送FIN bit = 1的TCP段
- 一旦接收到FIN，用ACK回应
  - 接到FIN段，ACK可以和它自己发出的FIN段一起发送
- 可以处理同时的FIN交换
- “对称释放，并不完美”

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726124018451.png" />

假设Client端发起中断连接请求，也就是发送FIN报文。Server端接到FIN报文后，意思是说“我Client端没有数据要发给你了”，但是如果你还有数据没有发送完成，则不必急着关闭Socket，可以继续发送数据。所以你先发送ACK，告诉Client端：“你的请求我收到了，但是我还没准备好，请继续你等我的消息”。这个时候Client端就进入FIN_WAIT状态，继续等待Server端的FIN报文。当Server端确定数据已发送完成，则向Client端发送FIN报文，告诉Client端：“好了，我这边数据发完了，准备好关闭连接了”。Client端收到FIN报文后，就知道可以关闭连接了，但是他还是不相信网络，怕Server端不知道要关闭，所以发送ACK后进入TIME_WAIT状态，如果Server端没有收到ACK则可以重传。Server端收到ACK后，就知道可以断开连接了。Client端等待了2MSL后依然没有收到回复，则证明Server端已正常关闭，那好，我Client端也可以关闭连接了。Ok，TCP连接就这样关闭了！

【问题1】为什么连接的时候是三次握手，关闭的时候却是四次握手？    
答：因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，“你发的FIN报文我收到了”。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。

## 3.6 拥塞控制原理

拥塞：
- 非正式的定义：“太多的数据需要网络传输，超过了网络的处理能力”
- 与流量控制不同，流量控制是我到你的问题，拥塞控制是网络的问题
- 拥塞的表现：
    - 分组丢失（路由器缓冲区溢出）
    - 分组经历比较长的延迟（在路由器的队列中排队）
- 网络中前10位的问题！

拥塞的原因/代价：场景1
- 2个发送端，2个接收端
- 1个路由器，具备无限大的缓冲
- 输出链路带宽： $R$ 
- 没有重传
- 每个连接的最大吞吐量： $R/2$ ；当进入的速率 $\lambda_{in}$ 接近链路链路带宽 $R$ 时，延迟增大（此时流量强度趋于1）

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726125936998.png" />

拥塞的原因/代价：场景2
- 1个路由器，有限的缓冲
- 分组丢失时，发送端重传
    - 应用层的输入=应用层输出： $\lambda_{in} = \lambda_{out}$
    - 传输层的输入包括重传： $\lambda_{in}^{'} \geq \lambda_{in}$，且当流量强度越趋于1， $\lambda_{in}^{'}$ 将比 $\lambda_{in}$ 大得多（很多都需要重传）

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726130345272.png" />

场景2：
1. 理想化：发送端有完美的信息
- 发送端知道什么时候路由器的缓冲是可用的
    - 只在缓冲可用时发送
    - 不会丢失：$\lambda_{in}^{'} = \lambda_{in}$
2. 理想化：掌握丢失信息
- 分组可以丢失，在路由器由于缓冲器满而被丢弃
- 如果知道分组丢失了，发送方重传分组
3. 现实情况：重复
- 分组可能丢失，由于缓冲器满而被丢弃
- 发送端最终超时，发送第2个拷贝，2个分组都被传出
- 此时随着 $\lambda_{in}$ 的增大， $\lambda_{out}$ 也增大，但是由于超时重传的比例越来越大， $\lambda_{out}$ 越来越小于 $\lambda_{in}$
    - 输出比输入少原因：1）重传的丢失分组；2）没有必要重传的重复分组

拥塞的“代价”：
- 延时大
- 为了达到一个有效输出，网络需要做更多的泵入（重传）
- 没有必要的重传，链路中包括了多个分组的拷贝
    - 是那些没有丢失，经历的时间比较长（拥塞状态）但是超时的分组，重发没有必要，对本就拥塞的网络雪上加霜（网络加速变坏，非线性）
    - 降低了的“goodput”

拥塞的原因/代价：场景3
- 4个发送端
- 多重路径
- 超时/重传
- Q：当 $\lambda_{in}^{'}$ 、 $\lambda_{in}$ 增加时，会发生什么？    
    A：当红色的 $\lambda_{in}^{'}$ 增加时，所有到来的蓝色分组都在最上方的队列中丢弃了，蓝色吞吐->0

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726131141125.png" height=200/><img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726131409102.png" height=200 />

又一个拥塞的代价：当分组丢失时，任何“关于这个分组的上游传输能力”都被浪费了，严重时导致整个网络死锁

2种常用的拥塞控制方法：
- **端到端拥塞控制**（自身判断）
    - 没有来自网络的显式反馈
    - 端系统根据延迟和丢失事件自身推断是否有拥塞
    - TCP采用的方法
- **网络辅助的拥塞控制**（路由器反馈）
    - 路由器提供给端系统以反馈信息
        - 单个bit置位，显示有拥塞 (SNA, DECbit, TCP/IP ECN, ATM)
        - 显式提供发送端可以采用的速率

> 案例：ATM ABR 拥塞控制（网络辅助的拥塞控制）
> 
> ATM网络数据交换的单位叫 信元（一个小分组，53字节：5字节的头部+48字节的数据载荷）。
> 
> 信元每个分组数据量较小，在每个交换节点存储-转发耽误的时间比分组交换小，且时间固定，便于调度。ATM网络集合了分组交换和线路交换的特性。
> 
> ATM虽然不是大众常用网络，但是在一些专用场合用处较大
> 
> ATM网络有很多模式，其中一个模式是ABR: available bit rate:
> - 提供“弹性服务” 
> - 如果发送端的路径“轻载”不发生拥塞
>     - 发送方尽可能地使用可用带宽（“市场调控”）
> - 如果发送方的路径拥塞了
>     - 发送方限制其发送的速度到一个最小保障速率上（“配给制”）
> 
> ATM网络中的信元可以分为2种：大部分是数据信元，另一种为间或地插入到数据信元当中的资源管理信元。
> 
> RM(资源管理)信元:
> - 由发送端发送，在数据信元中间隔插入
> - RM信元中的比特被交换机设置（“网络辅助”）
>     - NI bit: no increase in rate （轻微拥塞）速率不要增加了，轻微拥塞时设置为1
>     - CI bit: congestion indication 拥塞指示，拥塞时设置为1
> - 发送端发送的RM信元被接收端返回，接收端不做任何改变
> - 在RM信元中的2个字节ER(explicit rate)字段
>     - 拥塞的交换机可能会降低信元中ER的值
>     - 发送端发送速度因此是最低的可支持速率
> - 数据信元中的EFCI bit：被拥塞的交换机设置成1
>     - 如果在管理信元RM前面的数据信元EFCI被设置成了1，接端在返回的RM信元中设置CI bit
> 
> 总结：网络提供一些信息，包括一些标志位的置位以及字段 （为两主机间的通信提供多大的带宽）

## 3.7 TCP拥塞控制

- TCP拥塞控制采用端到端的拥塞控制机制
    - 路由器不向主机有关拥塞的反馈信息
        - 路由器的负担较轻
        - 符合网络核心简单的TCP/IP架构原则，网络核心提供最小的服务集
    - 端系统根据自身得到的信息，判断是否发生拥塞，从而采取动作
- 拥塞控制的几个问题
    - 如何检测拥塞
        - 轻微拥塞
        - 拥塞
    - 控制策略
        - 在拥塞发送时如何动作，降低速率
            - 轻微拥塞，如何降低
            - 拥塞时，如何降低
        - 在拥塞缓解时如何动作，尽可能增加速率

拥塞感知：发送端如何探测到拥塞？
- 某个段超时了（丢失事件）：拥塞
    - 超时时间到，某个段的确认没有来
    - 原因1：网络拥塞（某个路由器缓冲区没空间了，被丢弃） 概率大
    - 原因2：出错被丢弃了（各级错误，没有通过校验，被丢弃） 概率小。此时不应该降低发送速率
    - 一旦超时，就认为拥塞了，有一定误判（原因2，但概率小），但是总体控制方向是对的
- 有关某个段的3次重复ACK：轻微拥塞
    - 段的第1个ack，正常，确认绿段，期待红段
    - 段的第2个重复ack，意味着红段的后一段收到了，蓝段乱序到达
    - 段的第2、3、4个ack重复，意味着红段的后第2、3、4个段收到了，橙段乱序到达，同时红段丢失的可能性很大（后面3个段都到了，红段都没到）
    - 网络这时还能够进行一定程度的传输，拥塞但情况要比第一种好

速率控制方法：如何控制发送端发送的速率
- 维持一个拥塞窗口的值：CongWin（以字节为单位，表示发送方在对方未确认的情况下往网络中注入多少字节）
- 发送端限制已发送但是未确认的数据量（的上限）：
$$LastByteSent - LastByteAcked \leq CongWin$$
- 从而粗略地控制发送方的往网络中注入的速率

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20210726150610364.png" style="zoom:80%"/>

- CongWin是动态的，是感知到的网络拥塞程度的函数
    - 超时或者3个重复ack，CongWin下降
        - 超时：CongWin降为1MSS，进入SS阶段然后再倍增到CongWin/2（每个RTT），从而进入CA阶段
        - 3个重复ack：CongWin降为CongWin/2, CA阶段
    - 否则（正常收到Ack，没有发送以上情况）：CongWin跃跃欲试（上升）
        - SS阶段（慢启动阶段）：每经过一个RTT进行CongWin的加倍
        - CA阶段（拥塞避免阶段）：每经过一个RTT进行1个MSS的线性增加

TCP拥塞控制和流量控制的联合动作
- 联合控制的方法：
    - 发送端控制发送但是未确认的量同时也不能够超过接收窗口的空闲尺寸（接收方在返回给发送方的报文中捎带），满足流量控制要求
        - $SendWin = \min(CongWin, RecvWin)$
        - 同时满足 拥塞控制 和 流量控制 要求

TCP 拥塞控制：策略概述
- 慢启动
    - 连接刚建立， $CongWin = 1MSS$
        - 如： $MSS = 1460bytes$ & $RTT = 200msec$
        - 则可计算得初始速率为 $MSS/RTT = 58.4kbps$ ，这是一个很小的速率，可用带宽可能远远大于它，应当尽快着手增速，到达希望的速率
    - 当连接开始时，指数性（后一次速率是前一次的2倍）增加发送速率，直到发生丢失的事件
        - 启动初值很低
        - 但是速度很快（指数增加，SS时间很短，长期来看可以忽略）
    - 当连接开始时，指数性增加（每个RTT）发送速率直到发生丢失事件
        - 每一个RTT，CongWin加倍
        - 每收到一个ACK时，CongWin加1（等价于每个RTT，CongWin翻倍）
        - 慢启动阶段：只要不超时或3个重复ack，一个RTT后CongWin加倍
- AIMD：线性增、乘性减少
    - 乘性减：
        - 丢失事件后将CongWin降为1，将CongWin/2作为阈值，进入慢启动阶段（倍增直到CongWin/2）
    - 加性增：
        - 当CongWin>阈值时，一个RTT如没有发生丢失事件，将CongWin加1MSS：探测
    - 当收到3个重复的ACKs：（3个重复的ACK表示网络还有一定的段传输能力（轻微拥塞），超时之前的3个重复的ACK表示“警报”）
        - CongWin减半
        - 窗口（缓冲区大小）之后线性增长
    - 当超时事件发生时：
        - CongWin被设置成 1MSS，进入SS阶段
        - 之后窗口指数增长
        - 增长到一个阈值（上次发生拥塞的窗口的一半）时，再线性增加
- 超时事件后的保守策略
    - Q：什么时候应该将指数性增长变成线性？
    - A：在超时之前，当CongWin变成上次发生超时的窗口的一半
    - 实现：
        - 变量：Threshold
        - 出现丢失，Threshold设置成CongWin的1/2

总结：TCP拥塞控制
- 当CongWin < Threshold，发送端处于慢启动阶段(slow-start)，窗口指数性增长。
- 当CongWin > Threshold，发送端处于拥塞避免阶段(congestion-avoidance)，窗口线性增长。
- 当收到三个重复的ACKs(triple duplicate ACK)，Threshold设置成CongWin/2，CongWin=Threshold+3。
- 当超时事件发生时timeout，Threshold=CongWin/2，CongWin=1MSS，进入SS阶段

TCP 发送端拥塞控制
| 事件 | 状态 | TCP 发送端行为 | 解释|
| --- | --- | --- | --- |
| 以前没有收到ACK的data被ACKed | 慢启动(SS) | CongWin = CongWin + MSS <br> If (CongWin > Threshold) <br>    状态变成 “CA” | 每一个RTT，CongWin加倍 |
| 以前没有收到ACK的data被ACKed | 拥塞避免(CA) | CongWin = CongWin + MSS*(MSS/CongWin)| 加性增加，每一个RTT对CongWin加一个1MSS |
| 通过收到3个重复的ACK，发现丢失的事件 | SS or CA |Threshold = CongWin/2 <br> CongWin = Threshold+3 <br> 状态变成“CA” | 快速重传，实现乘性的减。<br> CongWin没有变成1MSS。|
| 超时 | SS or CA | Threshold = CongWin/2 <br> CongWin = 1MSS <br> 状态变成“SS” | 进入slow start|
| 重复的ACK | SS or CA | 对被ACKed的segment，增加重复ACK的计数 | CongWin and Threshold 不变 |

TCP 吞吐量
- TCP的平均吞吐量是多少，使用窗口window尺寸W和RTT来描述？
    - 忽略慢启动阶段，假设发送端总有数据传输
- 定义W为发生丢失事件时的窗口尺寸（单位：字节），则全过程在W/2和W之间变化
    - 平均窗口尺寸（#in-flight字节）：3/4W
    - 平均吞吐量：RTT时间吞吐3/4W 
        $$avg TCP thruput = \frac{3}{4} \frac{W}{RTT} bytes/sec$$

TCP公平性
- 公平性目标：如果 $K$ 个TCP会话分享一个链路带宽为 $R$ 的瓶颈，每一个会话的有效带宽为 $R/K$ 
- TCP为什么是公平的？
    - 考虑2个竞争的TCP会话：
        - 加性增加，斜率为1，吞吐量增加
        - 乘性减，吞吐量比例减少
- 公平性和UDP
    - 多媒体应用通常不是用TCP
        - 应用发送的数据速率希望不受拥塞控制的节制
    - 使用UDP：
        - 音视频应用泵出数据的速率是恒定的，忽略数据的丢失
    - 研究领域：TCP友好性
- 公平性和并行TCP连接
    - 2个主机间可以打开多个并行的TCP连接
    - Web浏览器
    - 例如：带宽为R的链路支持了9个连接 
        - 如果新的应用要求建1个TCP连接，获得带宽R/10
        - 如果新的应用要求建11个TCP连接，获得带宽R/2

## 3.8 总结 & 展望

- 传输层提供的服务 
    - 应用进程间的逻辑通信
        - Vs 网络层提供的是主机到主机的通信服务 
    - 互联网上传输层协议：UDP TCP 
        - 特性 
- 多路复用和解复用 
    - 端口：传输层的SAP 
    - 无连接的多路复用和解复用 
    - 面向连接的多路复用和解复用 
- 实例1：无连接传输层协议 UDP 
    - 多路复用解复用
    - UDP报文格式
    - 检错机制：校验和
- 可靠数据传输原理
    - 问题描述
    - 停止等待协议
        - Rdt 1.0, 2.0, 2.1, 2.2, 3.0 
    - 流水线协议 
        - GBN
        - SR(Selective Repeat)
- 实例2：面向连接的传输层协议-TCP 
    - 概述：TCP特性 
    - 报文段格式
        - 序号，超时机制及时间
    - TCP可靠传输机制
    - 重传，快速重传
    - 流量控制
    - 连接管理
        - 三次握手
        - 对称连接释放
- 拥塞控制原理
    - 网络辅助的拥塞控制
    - 端到端的拥塞控制
- TCP的拥塞控制
    - AIMD
    - 慢启动
    - 超时之后的保守策略
- 展望下2章：
    - 离开网络“边缘”（应用层和传输层）
    - 深入到网络的“核心”
    - 2个关于网络层的章
        - 数据平面
        - 控制平面
