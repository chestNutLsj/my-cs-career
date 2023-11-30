网络层解决了一个网络如何到达另外一个网络的路由问题。在**一个网络内部**如何**由一个节点（主机或者路由器）到达另外一个相邻节点**则需要用到链路层的**点到点**传输功能

## 内容简介

目标：
- 理解数据链路层服务的原理：
    - 检错和纠错
    - 共享广播信道：多点接入（多路访问）
    - 链路层寻址
    - LAN:以太网、WLAN、VLANs
    - 可靠数据传输，流控制：解决！
- 实例和各种链路层技术的实现

网络节点的连接方式：一个子网中的若干节点是如何连接到一起的？
- 点到点连接（广域），只有封装/解封装的功能
- 多点连接：（局域），“一发全收”，有寻址和MAC问题，链路层的功能更加复杂，有两种方式实现：
    - 通过共享型介质，如同轴电缆
    - 通过网络交换机

数据链路层和局域网
- WAN：网络形式采用点到点链路
    - 带宽大、距离远（延迟大）
    - 带宽延迟积大
    - 如果采用多点连接方式
        - 竞争方式：一旦冲突代价大
        - 令牌等协调方式：在其中协调节点的发送代价大
- 点到点链路的链路层服务实现非常简单，封装和解封装
- LAN一般采用多点连接方式
    - 连接节点非常方便
    - 接到共享型介质上（或网络交换机），就可以连接所有其他节点
- 多点连接方式网络的链路层功能实现相当复杂
    - 多点接入：协调各节点对共享性介质的访问和使用
    - 竞争方式：冲突之后的协调；
    - 令牌方式：令牌产生，占有和释放等

## 6.1 链路层概述

- 一些术语：
    - 运行链路层协议的所有设备都称作：**节点 nodes**（包括主机、路由器、交换机、WiFi接入点、网桥等）
    - 沿着通信路径，连接相邻节点的通信信道：**链路 links**
        - 有线链路
        - 无线链路
        - 局域网，共享性链路
        - 为了将一个数据报从源主机传输到目的主机，数据报必须通过沿端到端路径上的各段链路传输；
    - 链路层协议的数据单元称为：**帧 frame**，封装链路层数据报；

数据链路层负责从一个节点通过链路将（**帧**中的）数据报发送到相邻的物理节点。     

>[! note] 链路层传递数据的步骤
> Consider sending a datagram from one of the wireless hosts to one of the servers.
> ![[60-Link-layer-and-LAN-wireless-link-layer.png]]
> This datagram will actually pass through ***six links***:
> - a WiFi link between sending host and WiFi access point,
> - an Ethernet link between the access point and a link-layer switch;
> - a link between the link-layer switch and the router,
> - a link between the two routers;
> - an Ethernet link between the router and a link-layer switch; 
> - and finally an Ethernet link between the switch and the server.
> 
> Over a given link, a transmitting node encapsulates the datagram in a link-layer frame and transmits the frame into the link.

链路层：上下文
- 数据报（分组）在不同的链路上以不同的链路协议传送：
    - 第一跳链路：以太网
    - 中间链路：帧中继链路
    - 最后一跳802.11：无线网络协议
- 不同的链路协议提供不同的服务

> 传输类比
> - 从Princeton到Lausanne
>     - 轿车：Princeton to JFK
>     - 飞机：JFK to Geneva
>     - 火车：Geneva to Lausanne
> - 旅行者 = 数据报 datagram
> - 交通段 = 通信链路 communication link
> - 交通工具 = 链路层协议：数据链路层和局域网protocol
> - 票务代理 = 路由算法 routing algorithm

### 链路层提供的服务
> 一般化的链路层服务，不是所有的链路层都提供这些服务，一个特定的链路层只是提供其中一部分的服务（子集）

- 成帧：
    - 将网络数据报封装在帧中的数据字段，再加上帧头、帧尾部（具体的结构由链路层协议规定）
    - 在帧头部使用“MAC”（物理）地址来标示源和目的
        - 不同于IP地址

- 链路接入：
    - MAC (Medium Access Control, 媒体访问控制)协议规定了帧在链路上传输的规则；
    - 在链路两端各自仅有一个发送方或接收方的点对点链路，MAC 允许只要链路空闲，就可以发送帧；
    - 若是多个节点共享单个广播链路，MAC 则会协调多个节点的帧传输。

- 可靠交付：
    - 在（一个网络内）相邻两个节点完成可靠数据传递——保证无差错地经链路层移动每个网络层数据报；
    - 与传输层的可靠交付类似，链路层可靠交付的实现也是通过确认+重传
    - 在低出错率的链路上（光纤和双绞线电缆）很少使用——有线链路层协议通常不提供可靠交付服务
		- 出错率低，没有必要在每一个帧中做差错控制的工作，协议复杂
			- 发送端对每一帧进行差错控制编码，根据反馈做相应的动作
			- 接收端进行差错控制解码，反馈给发送端（ACK，NAK）
			- 在本层放弃可靠控制的工作，在网络层或者是传输层做可靠控制的工作，或者根本就不做可靠控制的工作
    - 在高差错链路上需要进行可靠的数据传送
        - 高差错链路：无线链路
        - Q：为什么要在采用无线链路的网络上，链路层做可靠数据传输工作，还要在传输层做端到端的可靠性工作？
        - 原因：出错率高，如果在链路层不做差错控制工作，漏出去的错误比较高；到了上层如果需要可靠控制的数据传输代价会很大

- 流量控制：
    - 使得相邻的发送和接收方节点的速度匹配

- 差错检测与纠正
    - 差错由信号衰减和噪声引起，导致接收方的硬件在判断电平时认错
    - 传输层和网络层也有有限的差错检测功能，但链路层的差错检测通常由硬件实现，也更复杂；
    - 接收方检测出的错误： 
        - 通知发送端进行重传或丢
    - 错误不太严重时，接收端检查和根据网络编码纠正bit错误，不通过重传来纠正错误

- 半双工和全双工：
    - 半双工：链路可以双向传输，但一次只有一个方向

### 链路层在何处实现？

![[60-Link-layer-and-LAN-Network-Adapter.png]]

- 链路层主体功能在“网络适配器”上实现
    - 也叫 network interface card, NIC
    - 或者在一个芯片组上，核心部分是链路层控制器
    - 以太网卡，802.11网卡；以太网芯片组
    - 实现链路层和相应的物理层功能
    - 在每一个主机上，网卡实现链路层和物理层的功能
        - 也在每个路由器上，插多个网卡，实现链路层和相应物理层的功能
        - 交换机的每个端口上
        - 接到主机的系统总线上
    - 硬件、软件和固件的综合体
        - 其中软件组件实现了高层链路层的功能，如组装链路层寻址信息、激活控制器硬件等；接收端链路层软件响应控制器中断、处理差错和传递数据给网络层；
        - 链路层是硬件和软件分界的位置。

适配器（网卡）通信
- 发送方：
    - 在帧中封装数据报
    - 加上差错控制编码，实现RDT和流量控制功能等
    - 交给物理层打出
- 接收方
    - 从物理层接收bit
    - 检查有无出错，执行rdt和流量控制功能等
    - 解封装数据报，将帧交给上层

## 6.2 差错检测和纠正

![[60-Link-layer-and-LAN-EDC.png]]
- EDC = 差错检测和纠正位（冗余位）
- D = 数据（由差错检测保护，可以包含头部字段）
- 错误检测不是100%可靠的！
    - 协议会漏检一些错误，但是很少
    - 更长的EDC字段可以得到更好的检测和纠正效果

### 奇偶校验

- 加一个校验位，使得整个出现的1的个数是奇数还是偶数，是奇数->奇校验，是偶数->偶校验
- Receiver operation is also simple with a single parity bit. The receiver need only count the number of 1s in the received d+1 bits. *If an odd number of 1-valued bits are found with an even parity scheme, the receiver knows that at least one bit error has occurred*. More precisely, it knows that some odd number of bit errors have occurred.
> 以奇校验为例，接收方只需检查收到的数据是否仍是奇数个 1。
> 这种校验方式能够检验奇数个位的错误。
> 然而，如果是偶数个位的错误，则 1的数量仍是奇数个，因此无法检验。

- If the probability of bit errors is small and errors can be assumed to occur independently from one bit to the next, the probability of multiple bit errors in a packet would be extremely small. In this case, a single parity bit might suffice. However, measurements have shown that, rather than occurring independently, errors are often clustered together in “bursts.” Under burst error conditions, the probability of undetected errors in a frame protected by single-bit parity can approach 50 percent `[Spragins 1991]`.
> 如果位发生错误之间是独立的且概率较低，那么单个位奇偶校验足够有用。
> 但是研究显示，错误会突发地集中在某个区域内出错，这种情况下单个位奇偶校验的成功率仅仅是 50%。

- 二维奇偶校验：
	- ![[60-Link-layer-and-LAN-two-dimensional-even-parity.png]]
	- Figure 6.5 shows a two-dimensional generalization of the single-bit parity scheme. Here, the *d* bits in *D* are divided into *i* rows and *j* columns. A parity value is computed for each row and for each column. The resulting *i + j + 1* parity bits comprise the link-layer frame’s error-detection bits.
	- Suppose now that a single bit error occurs in the original d bits of information. With this ***two-dimensional parity*** scheme, the parity of both the column and the row containing the flipped bit will be in error. The receiver can thus not only detect the fact that a single bit error has occurred, but can use the column and row indices of the column and row with parity errors to actually identify the bit that was corrupted and correct that error!（二维奇偶校验策略位每一行和每一列都设置奇偶校验位，因此一个位出错会在行和列同时显示，也因此具有定位出错位的位置的能力）
	- Figure 6.5 shows an example in which the 1-valued bit in position (2,2) is corrupted and switched to a 0—an error that is both detectable and correctable at the receiver. Although our discussion has focused on the original d bits of information, a single error in the parity bits themselves is also detectable and correctable.
	- Two-dimensional parity can also detect (but not correct!) any combination of two errors in a packet.（二维奇偶校验也能够检测到一个分组中任意两位的错误，但是无法纠正）

### 校验和

- The ***Internet checksum*** is that bytes of data are treated as 16-bit integers and summed. The 1s complement of this sum then forms the Internet checksum that is carried in the segment header.
- As discussed in Section 3.3, the receiver checks the checksum by taking the 1s complement of the sum of the received data (including the checksum) and checking whether the result is all 0 bits. If any of the bits are 1, an error is indicated. `RFC 1071` discusses the Internet checksum algorithm and its implementation in detail.
> 校验和是将字节流数据视作 16bit 整数相加，然后取对 1 的补码填入分组首部的校验和字段。
> 当接收方收到分组时，通过校验和字段与数据字段相加进行检验——结果是 0 表明没有错，否则意味着差错发生。

- In the TCP and UDP protocols, the Internet checksum is computed over all fields (header and data fields included).
- In IP, the checksum is computed over the IP header (since the UDP or TCP segment has its own checksum).
- In other protocols, for example, XTP `[Strayer 1992]`, one checksum is computed over the header and another checksum is computed over the entire packet.
> TCP 和 UDP 的校验和会计算所有域（即包括首部和数据域），IP 只计算首部。

- 通常校验和用于 TCP 和 UDP 这样的传输层协议中，CRC 用于链路层协议中。这是因为传输层通过软件实现，因此简单的差错检测可以在不过分影响性能的情况下使用；而链路层的差错检测通过硬件实现，速度更快，因此可以使用 CRC 这样繁琐的差错检测方案。

### 循环冗余校验

1. 模2运算（加法不进位，减法不借位，位和位之间没有关系）：同0异1，异或运算
	- 模 2 加减都相当于 XOR 异或运算
	- 模 2 除与算术除类似，从被除数最高位开始，如果最高位（包括运算过程中的情况）是 1，则商的该位是 1，其余位做模 2 减；如果最高位是 0，则商的该位是 0.
	- 模 2 乘，乘 $2^r$ 相当于左移 r 位。

2. 位串的两种表示：位串 or 多项式 的表示方式
$$1011 \iff 1 * x^3 + 0 * x^2 + 1 * x^1 + 1 * x^0 = x^3 + x + 1$$

3. 生成多项式：r+1 位（发送方和接收方要预先约定好），记为 *G*

4. 在发送方发送的 D 位数据比特后附上 r 位的冗余位 R，使得序列正好被生成多项式整除，则没有出错
	- ![[60-Link-layer-and-LAN-CRC.png]]
	- 将数据比特 D，看成是 d 位二进制的数据
	- 生成多项式 G：双方协商 r+1 位模式串
	- 选择 r 位 CRC 附加位 R，得到 D+R 模式串（d+r 位模式串），其正好能被 G 整除 (mod 2) 
	- 接收方知道 G，将收到的 d+r 位模式串除以 G，如果非 0 余数：检查出错误！
	- 用公式表示上述过程，即
		- 发送方进行的操作是 $\text{D} \times 2^{r} \text{ XOR R} = n\text{G}$，
		- 接收方则对该式两边都用 R 异或—— $\text{D}\times 2^{r}=n\text{G XOR R}$，其含义就是如果用 G 来除 $\text{D}\times 2^r$，余数值刚好是 R
		- 即 $\text{R}=\text{remainder} \frac{D\times 2^{r}}{G}$ 

>[! example] CRC 实例
> ![[60-Link-layer-and-LAN-CRC-calc.png]]
> Figure 6.7 illustrates this calculation for the case of D = 101110, d = 6, G = 1001, and r = 3. The 9 bits transmitted in this case are 101110 011.
> 
> You should check these calculations for yourself and also check that indeed $D \times 2^{r} = 101011 \times G \text{ XOR } R$.

5. International standards have been defined for 8-, 12-, 16-, and 32-bit generators, G. The CRC-32 32-bit standard, which has been adopted in a number of link-level IEEE protocols, uses a generator of $$G_{CRC-32} = 100000100110000010001110110110111$$

7. CRC 性能分析
	- 突发错误和突发长度
	- CRC检错性能描述
	- 能够检查出所有的1bit 错误
	- 能够检查出所有的双 bits 的错误
	- 能够检查出所有长度小于等于 r 位的错误
	- 出现长度为 r+1 的突发错误，检查不出的概率是 $\frac{1}{2^{r-1}}$
	- 出现长度大于 r+1 的突发错误，检查不出的概率是 $\frac{1}{2^r}$

## 6.3 多点访问链路和协议

> 点到点不存在多点访问的问题（两个访问端都确定好了），多点连接则需要考虑。

### 链路类型

- 两种类型的链路（一个子网内部链路连接形式）：
    - 点对点链路
        - 链路两端的接收方和发送方各自只有 1 个设备
        - 拨号访问的 PPP（Point-to-Point Protocol，点对点协议）
        - 高级数据链路控制 HDLC（High-level Data Link Control）
    - 广播，也称为多点连接的网络
        - 多个发送或接收节点连接到相同、单一、共享的信道
        - 广播的含义，即任何一个节点传输一个帧时，信道广播该帧，链路上其他节点都收到一个副本
        - 举例：
            - 传统以太网（同轴电缆连接所有节点，所有节点通过比较 MAC 地址确认帧发送的目标地址）
            - HFC 上行链路
            - 802.11无线局域网

### 多路访问控制协议

多路访问协议
- 作用：规范、处理共享的广播信道上的传输行为
- 冲突/碰撞 (collision)：
	- 多个节点在同一个时刻发送，则接收方会收到 2 个或多个信号叠加
	- 碰撞发生时，所有接收节点都无法获得有效的传输帧，即碰撞帧将所有帧都污染了；
	- 碰撞发生就会极大地浪费广播信道的带宽，因此决定节点如何使用共享信道以致碰撞频率降低，可以很大程度地提高广播信道的带宽利用率。
- **共享控制**：传输控制信息，使得各节点协调信道的使用
    - 没有带外的信道，只有这一个信道，即要占用信道本身的带宽；

理想的多路访问协议
- 给定：*R* bps 的广播信道
- 必要条件：
    1. 当一个节点要发送时，可以 R 速率发送 —— 满速
    2. 当 M 个节点要发送，每个可以以 R/M 的平均速率发送 —— 公平、均分
    3. 分布式的：不会因为中心节点的崩溃而导致系统崩溃
    4. 简单又好用

MAC（多路访问控制，既可以是 multiple，也可以是 medium）协议：
- 分类：3大类：
	- 信道划分 (partition)
		- 把信道划分成小片（时间、频率、编码）
		- 分配片给每个节点专用
	- 随机访问 (random)：“想用就用”
		- 信道不划分，允许冲突/碰撞
		- 检测冲突，冲突后恢复
	- 依次轮流：分为 完全分布式的（令牌方式） 和 主节点协调式的（主节点轮流询问）
		- 节点依次轮流
		- 但是有很多数据传输的节点可以获得较长的信道使用权（发送数据较长者）

#### 信道划分协议

- TDMA(time division multiple access)分时复用
    - 将时间划分为时间帧 time frame，进一步划分每个时间帧为 N 个时隙 slot；
    - 每个站点使用每周期中固定的时隙（长度=帧传输时间）传输数据帧
    - 通常时隙的长度要能容纳一个数据帧；
    - 优势：每个节点得到的传输速率都是 R/N bps，绝对公平
    - 缺点：
        - 如果站点无帧传输，时隙空闲->浪费 （改进方法，统计时分复用）
        - 即使只有一个节点在使用信道，也只能获得 R/N bps 的传输速率
        - 节点必须等待传输序列中的轮次才能发送数据帧

![[60-Link-layer-and-LAN-TDM-FDM.png]]

- FDMA(frequency division multiple access)频分复用
    - 信道的有效频率范围被分成一个个小的频段
    - 每个站点被分配一个固定的频段
    - 分配给站点的频段如果没有被使用，则空闲
    - 优缺点与 TDMA 类似；

- CDMA(code division multiple access)码分复用
    - 有站点在整个频段上同时进行传输，采用编码原理加以区分
    - 如果编码理想地不会冲突，那么不同节点同时传输的问题即刻解决——各自相应的接收方仍能正确接收来自发送方的数据帧
    - 假定：信号同步很好，线性叠加
    - 应用：蜂窝电话（无线信道）

- 比方
    - TDM：不同的人在不同的时刻讲话
    - FDM：不同的组在不同的小房间里通信
    - CDMA：不同的人使用不同的语言讲话

#### 随机接入协议

- 当节点有帧要发送时
    - 总是以信道带宽的全部 R bps 发送，遇到碰撞就反复地重发帧，直到该帧不碰撞地正确传输（当然不是立即重发，而是涉及到碰撞的节点，就各自独立地随机等待一段时间后重发）
    - 没有节点间的预先协调
- 两个或更多节点同时传输，会发生->冲突“collision”
- 随机存取协议规定: 
    - 如何检测冲突
    - 如何从冲突中恢复（如：通过重传）
    - 随机MAC协议：
        - 时隙ALOHA
        - 纯ALOHA（非时隙）
        - CSMA, CSMA/CD, CSMA/CA

1. **时隙 ALOHA** 协议
	- 假设：
		- 所有帧是等长的 L 比特，能够持续一个时隙
		- 时间被划分成相等的时隙（长度为 L/R 秒），每个时隙可发送一帧
		- 节点只在时隙开始时发送帧
		- 共享信道的所有节点在时钟上是同步的——每个节点都知道时隙何时开始
		- 如果两个或多个节点在一个时隙中碰撞，所有的站点在该时隙结束之前都能检测到碰撞事件
	- 运行：
		- 当节点有一个新的帧要发送，在下一个时隙内传输整个帧
		- 传输时没有检测到冲突（可从信道内的信息能量的幅度判断），成功，并且节点能够在下一时隙发送新帧
		- 如果发生碰撞，则节点在时隙结束前检测到这次碰撞，只有在每一个随后的时隙以概率 p 重传帧直到成功（可能仍然发生冲突，但是时间越长，冲突概率越低）
			- 这里概率 p，指的是几何分布——每次判断，有 p 的概率重发后不冲突，直到n次后重发成功的总概率
	- 优点
		- 节点可以以信道带宽全速连续传输，尤其是在只有一个节点占用信道时
		- 高度分散：每个节点检测碰撞、独立地决定什么时候重传
		- 简单
	- 缺点
		- 时隙ALOHA需要在节点中对时隙同步
		- 存在冲突，浪费时隙
		- 即使有帧要发送，仍然有可能存在空闲的时隙
		- 节点检测冲突的时间 小于 帧传输的时间
		- 必须传完
	- 效率 (Efficiency)
		- 定义：当有大量活跃节点且每个节点都由大量的帧需要发送，长期运行中成功时隙所占的份额。（成功时隙指的是恰好只有一个节点传输的时隙）
		- ![[60-Link-layer-and-LAN-slot-ALOHA.png]]
		- 假设 N 个节点，每个节点都有很多帧要发送，在每个时隙中的传输概率是 $p$ 
		- 一个节点成功传输概率是 $p(1-p)^{N-1}$ （几何分布）
		- 任何一个节点的成功概率是 $N \times p(1-p)^{N-1}$
		- $N$ 个节点的最大效率：求出使 $f(p) = N \times p(1-p)^{N-1}$ 最大的 $p^{ ' }$
		- 代入 $p^{ ' }$ 得到最大 $f(p^{ ' }) = N \times p^{ ' }(1-p^{ ' })^{N-1}$ 
		- $N$ 为无穷大时的极限为 $1/e=0.37$ ，即最好情况：信道利用率 $37\%$ 


2. **纯 ALOHA**（非时隙）：数据帧一形成立即发送
	- 无时隙 ALOHA：简单、无须节点间在时间上同步（时隙 ALOHA 需要在时隙上同步，每个时隙开始时进行传输）
	- 当有帧从网络层传递下来，就立刻传输；如果一个传输的帧与其它帧的传输发生了碰撞，这个节点在完整传输完碰撞帧后，立即以概率 p 尝试重传，否则等待一个帧传输时间；
	- 冲突的概率增加:
		- 帧在 $t_0$ 发送，和其它在 $[t_0-1, t_0+1]$ 区间内开始发送的帧冲突
		- 和当前帧冲突的区间（其他帧在此区间开始传输）增大了一倍
		- ![[60-Link-layer-and-LAN-ALOHA.png]]
	- 纯ALOHA的效率$$
\begin{split}
P(\text{指定节点成功}) &= P(\text{节点传输}) \times \\ 
&\quad P(\text{其它节点在 $[t_0-1, t_0]$ 不传}) \times \\ 
&\quad P(\text{其它节点在 $[t_0, t_0+1\text{不传}]$ }) \\ 
&= p(1-p)^{N-1}(1-p)^{N-1} \\ 
&= p(1-p)^{2(N-1)}
\end{split}
$$
		- 选择最佳的 $p$ 且 $N$ 趋向无穷大时，效率为 $\frac{1}{2e} = 17.5\%$ ，效率比时隙 ALOHA 更差！

>[! note] ALOHA 的特点与改进目标——CSMA
> ALOHA 协议的特点就是：在发送自己的帧之前不查看链路的情况，这种情况既自私又无礼——把公共链路当成自己独占的；
> 
> 因此改进方向就是在发送前先查看链路的状态，如果空闲再发送，这就引出了两种思想：
> - ***Listen before speaking***. If someone else is speaking, wait until they are finished. In the networking world, this is called carrier sensing—a node listens to the channel before transmitting. If a frame from another node is currently being transmitted into the channel, a node then waits until it detects no transmissions for a short amount of time and then begins transmission. —— 载波侦听
> - ***If someone else begins talking at the same time, stop talking***. In the networking world, this is called ***collision detection***—a transmitting node listens to the channel while it is transmitting. If it detects that another node is transmitting an interfering frame, it stops transmitting and waits a random amount of time before repeating the sense-and-transmit-when-idle cycle. —— 碰撞检测

3. ***CSMA***(载波侦听多路访问) —— “说之前先听”
	- CSMA：在传输前先侦听信道：
		- 如果侦听到信道空闲，传送整个帧
		- 如果侦听到信道忙，推迟传送
	- CSMA 冲突
		- 冲突仍然可能发生——由传播延迟造成：两个节点可能侦听不到正在进行的传输
	- 冲突：
		- 整个冲突帧的传输时间都被浪费了，是无效的传输
	- 本质：
		- ==传播延迟（距离）决定了冲突的概率==
		- 本质上是节点依据本地的信道使用情况来判断全部信道的使用情况——距离越远，延时越大，发生冲突的可能性就越大
	- 举例：
		- ![[60-Link-layer-and-LAN-CSMA.png]]
		- Figure 6.12 shows a space-time diagram of four nodes (A, B, C, D) attached to a linear broadcast bus. The horizontal axis shows the position of each node in space; the vertical axis represents time.
		- At time t0, node B senses the channel is idle, as no other nodes are currently transmitting. Node B thus begins transmitting, with its bits propagating in both directions along the broadcast medium. The downward propagation of B’s bits in Figure 6.12 with increasing time indicates that a nonzero amount of time is needed for B’s bits actually to propagate (albeit at near the speed of light) along the broadcast medium.（t0时刻，B 意识到链路空闲，于是开始传输帧，上图中蓝色区域向下、向两边蔓延，即数据在链路上向两侧传播，并且传播需要时间）
		- At time t1 (t1 > t0), node D has a frame to send. Although node B is currently transmitting at time t1, the bits being transmitted by B have yet to reach D, and thus D senses the channel idle at t1. In accordance with the CSMA protocol, D thus begins transmitting its frame. A short time later, B’s transmission begins to interfere with D’s transmission at D.（t1时刻 D 也有帧需要发送，此时也检测到链路空闲——实际上 B 发送的帧还未传播到 D 处——但是根据 CSMA 协议，D 仍然头铁地传输帧，于是碰撞发生了）
		- From Figure 6.12, it is evident that the end-to-end ***channel propagation delay*** of a broadcast channel—the time it takes for a signal to propagate from one of the nodes to another—will play a crucial role in determining its performance. The longer this propagation delay, the larger the chance that a carrier-sensing node is not yet able to sense a transmission that has already begun at another node in the network.（信道传播时延在此处至关重要，传播时延越长，载波侦听策略所不能帧听到远处已发送帧的节点的可能就越大）
	- 改进：CSMA --> CSMA/CD（目前有线介质的局域网如“以太网”采用的方式）

4. ***CSMA/CD***(冲突检测) —— “边说边听” 
	- CSMA 的缺点：只是在发送前进行碰撞检测，不能确定远端是否有帧已经开始发送；并且即使发送过程中出现了碰撞，还是头铁地将帧传输完；
	- 改进：加入碰撞检测
		- 在传输帧的同时，检测是否发生了碰撞，一旦检测到碰撞，就各自停止传输——减少对信道的浪费
		- ![[60-Link-layer-and-LAN-CSMA-CD.png]]
		- ![[60-Link-layer-and-LAN-CSMA-CD-flow.png]]
	- 更具体的步骤：
		1. The adapter obtains a datagram from the network layer, prepares a link-layer frame, and puts the frame adapter buffer.（从网络层获取的数据报在链路层打包成帧，然后放入帧适配器的缓存区中）
		2. If the adapter senses that the channel is idle (that is, there is no signal energy entering the adapter from the channel), it starts to transmit the frame. If, on the other hand, the adapter senses that the channel is busy, it waits until it senses no signal energy and then starts to transmit the frame.（如果适配器检测到链路空闲，就开始传输帧；否则就等待，直到链路不忙）
		3. While transmitting, the adapter monitors for the presence of signal energy coming from other adapters using the broadcast channel.（传输帧过程中，适配器监视信道，查看是否有来自其它适配器的信号）
		4. If the adapter transmits the entire frame without detecting signal energy from other adapters, the adapter is finished with the frame. If, on the other hand, the adapter detects signal energy from other adapters while transmitting, it aborts the transmission, and transmits a ***jam*** signal.（如果适配器传输完整的帧且没有检测到其它适配器的能量，则该次传输帧完成；否则丢弃当前的传输，并传输一个 *jam* 信号，表示当前信道已被占用）
			- *jam* 信号的作用：强化冲突，确保整条链路上的所有接收者都能检测到碰撞的发生。
		5. After aborting, the adapter waits a random amount of time and then returns to step 2.（停止当前传输后，适配器各自等待随机时间后再回到步骤 2，开始尝试新的传输）
	- 碰撞后等待的时间间隔如何设置？**二进制指数后退**！
		- 在第 n 次失败（发送时有碰撞）后，适配器随机选择一个 $\{ 0，1，2，3，……，2^{n-1}\}$ 中的 *K* 值，等待 `K*512` 比特时间（即 发送 512 比特进入以太网所需时间量的 *K* 倍），n 能够取得的最大值不超过 10；
		- 可选 K 的集合随着碰撞次数指数增长，而选取 K 的方式是等概率地随机选取，因此随着 n 的增大，能够成功发送的概率越来越大；（不成功就是有两个适配器选取的 K 值相同）
		- 另外有新的帧刚刚到达，而其它适配器正在退避过程中——新的帧会抢先发送，因为它直接运行了 CSMA/CD 策略；
		- 指数退避的特点：
			- 高负载（重传节点多）：重传窗口时间大，减少冲突，但等待时间长
			- 低负载（重传节点少）：使得各站点等待时间少，但冲突概率大
		- CSMA/CD 的效率：
			- 有大量活跃节点和每个节点都由大量帧要发送的理想情况下，帧在信道中无碰撞地传输时间在长期运行时间中的所占份额：
			- 令 $t_{prop}$ 为 LAN 上 2 个节点的最大传播延迟，$t_{trans}$ 为传输最大以太网帧的时间，则效率计算式为：
			- $$ efficiency = \frac{1}{1 + 5 \times t_{prop} / t_{trans}} $$
			- 当 $t_{prop}$ 变成 $0$ ——信道传播速度足够快，或当 $t_{trans}$ 变成无穷大——帧无穷大，将使效率增加并接近 1；

5. 无线局域网 CSMA/CA
	- 无线信道倾向于选择 CSMA/CA：因为无线信道中传输者在传输数据报的过程中，会关闭接收器，因而不适用 CSMA/CD；
	- 由于[隐藏节点问题](https://en.wikipedia.org/wiki/Hidden_node_problem)，CSMA/CA 并不是绝对可靠的；
		- ![[60-Link-layer-and-LAN-hidden-node-problem.png]]
		- In one scenario, Station A can communicate with Station B. Station C can also communicate with Access Point Station B. However, Stations A and C cannot communicate with each other as they are out of range of each other, and thus start to transmit simultaneously preventing B from receiving messages intended for it.
	- 碰撞避免的策略：
		- ![[60-Link-layer-and-LAN-CSMA-CA-flow.png]]
		- 发送方：
			- 如果站点侦测到信道空闲持续 DIFS 长，则传输整个帧
			- 如果侦测到信道忙碌，那么选择一个随机回退值，并在信道空闲时递减该值；如果信道忙碌，回退值不会变化；回退值递减到 0 时（只发生在信道闲时）发送整个帧。
			- 如果没有收到 ACK，增加回退值，重复这段的整个过程；
			- ![[60-Link-layer-and-LAN-CSMACA-DIFS.png]]
		- 802.11 接收方
			- 如果帧正确，则在 SIFS 后发送 ACK
		- *无线链路特性，需要每帧都请求、确认（有线网由于边发边确认，不需要接收 ACK 信息就可以知道是否发送成功），这里的失败原因还是在担心隐藏节点问题。*
	- 细节问题：
		- 在随机回退值 count down 时，侦听到了信道空闲为什么不发送，而要等到0时在发送？以下例子可以很好地说明这一点
		- 考虑 2 个站点有数据帧需要发送，第 3 个节点正在发送的情况：
		- 有线局域网使用CD：让 2 者听完第三个节点发完，立即发送
			- 冲突：放弃当前的发送，避免了信道的浪费于无用冲突帧的发送
			- 代价不昂贵
		- 无线局域网使用 CA：
			- 无法 CD，一旦发送就必须发完，如冲突信道浪费严重，代价高昂
			- 思想：尽量事先避免冲突，而不是在发生冲突时放弃然后重发
			- 听到发送的站点，分别选择随机值，回退到 0 发送
				- 不同的随机值，一个站点会胜利
				- 失败站点会冻结计数器，当胜利节点发完再发
	- 进一步地冲突避免：RTS-CTS 交换 —— 对长帧的可选项
		- ![[60-Link-layer-and-LAN-CSMA-CA-DIFS.png]]
		- 思想：允许发送方“预约”信道，而不是随机访问该信道：避免长数据帧的冲突（可选项）
		- 发送方首先使用CSMA向接收方发送一个小的RTS分组
			- RTS 可能会冲突（但是由于比较短，浪费信道较少）
		- 接收方广播 CTS，作为 RTS 的响应
			- CTS 能够被所有涉及到的节点听到
			- 正确的发送方能够发送数据帧，而其它节点抑制发送
		- 采用小的预约分组，可以完全避免数据帧的冲突

#### 轮流协议

- 回顾：
	- MAC 协议的两个理想目标：(1) when only one node is active, the active node has a throughput of R bps, and (2) when M nodes are active, then each active node has a throughput of nearly R/M bps.
	- 信道划分协议：固定分配、平分
		- 共享信道在高负载时是有效和公平的
		- 在低负载时效率低下：只能等到自己的时隙开始发送或者利用1/N 的信道频率发送，当只有一个节点有帧传时，也只能够得到1/N 个带宽分配
	- 随机访问MAC协议
		- 在低负载时效率高：单个节点可以完全利用信道全部带宽
		- 高负载时：冲突开销较大，效率极低，时间很多浪费在冲突中

- 轮流协议：有两种策略：轮询 polling 和令牌 token-passing
	- 轮询：
		- 从链路上所有节点中选出一个主节点，主节点以循环的方式轮询每个节点——In particular, the master node first sends a message to node 1, saying that it (node 1) can transmit up to some maximum number of frames. After node 1 transmits some frames, the master node tells node 2 it (node 2) can transmit up to the maximum number of frames. (The master node can determine when a node has finished sending its frames by observing the lack of a signal on the channel.) The procedure continues in this manner, with the master node polling each of the nodes in a cyclic manner.
		- 从节点没有自己的链路支配权；
		- 缺点：
			- 轮询开销：轮询本身消耗信道带宽
			- 轮询时延：通知一个节点可以开始传输的时间
			- 等待时间：每个节点需等到主节点轮询后开始传输，即使只有一个节点，也需要等到轮询一周后才能够发送
			- 单点故障：主节点失效时造成整个系统无法工作
	- 令牌传递：（没有主节点）
		- 控制令牌 (token)循环从一个节点到下一个节点传递 （“击鼓传花”）。如果令牌到达的节点需要发送信息，就将令牌位置位为 0，将令牌帧变为数据帧，发送的数据绕行一周后再由自己收下
			- 为什么不是由目标节点收下？“一发多收”，目标节点有多个，如果由目标节点收下会导致后面的目标节点收不到，所以需要轮转一圈，确认经过了所有节点后由自己收下
		- 令牌报文：特殊的帧
		- 缺点：
			- 令牌开销：本身消耗带宽
			- 延迟：只有等到抓住令牌，才可传输
			- 单点故障 (token)：
				- 一个节点的故障会导致整个信道故障——单向传输的弊端；因此改进目标就是双向传输，可是又涉及复杂的令牌设计问题
				- 令牌丢失系统级故障，整个系统无法传输
				- 复杂机制重新生成令牌

### DOCSIS：综合三种访问方式的例子

Recall from Section 1.2.1 that a cable access network typically connects several thousand residential cable modems to a cable modem termination system (CMTS) at the cable network headend. The Data-Over-Cable Service Interface Specifications (DOCSIS) specifies the cable data network architecture and its protocols.
> 电缆接入网通常将几千个住宅电缆调制解调器与**电缆调制解调器端系统**在电缆网头端连接，而 DOCSIS 接口规定了电缆数据网络体系结构和相关协议。

DOCSIS uses FDM to divide the downstream (CMTS to modem) and upstream (modem to CMTS) network segments into multiple frequency channels. Each downstream channel is between 24 MHz and 192 MHz wide, with a maximum throughput of approximately 1.6 Gbps per channel; each upstream channel has channel widths ranging from 6.4 MHz to 96 MHz, with a maximum upstream throughput of approximately 1 Gbps. Each upstream and downstream channel is a broadcast channel. Frames transmitted on the downstream channel by the CMTS are received by all cable modems receiving that channel; since there is just a single CMTS transmitting into the downstream channel, however, there is no multiple access problem. The upstream direction, however, is more interesting and technically challenging, since multiple cable modems share the same upstream channel (frequency) to the CMTS, and thus collisions can potentially occur.
> - DOCSIS 采用 FDM 进行信道的划分——将上行、下行网络段划分为多个不同频率的信道
> - 下行信道：
> 	- 信道频率在 24MHz 到192MHz，最大吞吐量 1.6Gbps per channel
> 	- 下行、上行信道都是广播信道，CMTS 在下行信道中传输的帧被信道上所有接收方调制解调器都能收到；
> 	- 单一的 CMTS 在下行信道传输可以避免多路访问问题；但反之上行信道的碰撞问题比较复杂；
> 	- 在下行 MAP 帧中：CMTS 告诉各节点微时隙分配方案，分配给各站点的上行微时隙
> 	- 另外：头端传输下行数据（给各个用户）
> - 上行信道：
> 	- 信道频率在 6.4MHz 到 96MHz，最大吞吐量约 1Gbps.
> 	- 采用 TDM 的方式将上行信道分成若干微时隙：MAP 指定
> 	- 站点采用分配给它的微时隙上行数据传输：分配
> 	- 在特殊的上行微时隙中，各站点请求上行微时隙：竞争
> 	- 各站点对于该时隙的使用是随机访问的
> 	- 一旦碰撞（请求不成功，结果是：在下行的 MAP 中没有为它分配，则二进制退避）选择时隙上传输

![[60-Link-layer-and-LAN-DOCSIS.png]]
As illustrated in Figure 6.14, each upstream channel is divided into intervals of time (TDM-like), each containing a sequence of mini-slots during which cable modems can transmit to the CMTS. The CMTS explicitly grants permission to individual cable modems to transmit during specific mini-slots. The CMTS accomplishes this by sending a control message known as a MAP message on a downstream channel to specify which cable modem (with data to send) can transmit during which mini-slot for the interval of time specified in the control message. Since mini-slots are explicitly allocated to cable modems, the CMTS can ensure there are no colliding transmissions during a mini-slot.
> 上行线路划分为时间间隔，每个时间间隔有一个微时隙序列，电缆 modem 在微时隙中向 CMTS 传输帧。
> CMTS 显式地允许每个电缆 modem 在特定的微时隙中传输——在下行信道中通过发送 MAP 报文，控制指定的电缆 modem 获得微时隙中传输指定时间间隔的权限。——轮询的思路，从而确保没有碰撞。

如何确定电缆 modem 有数据要发送？This is accomplished by having cable modems send mini-slot-request frames to the CMTS during a special set of interval mini-slots that are dedicated for this purpose, as shown in Figure 6.14. These mini-slot-request frames are transmitted in a random access manner and so may collide with each other. A cable modem can neither sense whether the upstream channel is busy nor detect collisions. Instead, the cable modem infers that its mini-slot-request frame experienced a collision if it does not receive a response to the requested allocation in the next downstream control message. When a collision is inferred, a cable modem uses binary exponentia backoff to defer the retransmission of its mini-slot-request frame to a future time slot. When there is little traffic on the upstream channel, a cable modem may actually transmit data frames during slots nominally assigned for mini-slot-request frames (and thus avoid having to wait for a mini-slot assignment).
> - 电缆 modem 在特定的微时隙间隔内，发送微时隙间隔请求——获取发送权。
> - 这些微时隙请求帧是随机接入的方式进行传输，因此可能互相碰撞。然而电缆 modem 既不能侦听上行信道是否繁忙，也不能检测碰撞。
> - 事实上，电缆 modem 如果没有在下一个下行控制报文中收到请求分配的响应，于是推断出微时隙请求帧发生了碰撞——当检测到碰撞，就使用二进制指数回退将微时隙请求帧延后重新发送。

## 6.4 交换局域网

链路层设备如链路层交换机等，不识别网络层地址，因此 RIP、OSPF 这类路由选择算法无法用于确定链路层帧的路径——因而需要链路层地址转发链路层帧。

### 链路层寻址和ARP

- MAC地址
    - IP地址：
        - 网络层地址
        - 前 n-1 跳：用于使数据报到达目的 IP 子网
        - 最后一跳：到达子网中的目标节点 —— IP地址的主机号部分
    - LAN（MAC/物理/以太网）地址：
        - 用于使帧从一个网卡传递到与其物理连接的另一个网卡（在同一个物理网络中）（注意到，并不是每个主机或路由器都由链路层地址，实际上链路层地址与适配器一一对应）
        - 48bit MAC地址固化在适配器的ROM，有时也可以通过软件设定
            - 理论上全球任何2个网卡的 MAC 地址都不相同
        - 链路层交换机的任务是在主机与路由器之间承载链路层帧，即主机或路由器不必明确地将帧寻址到具体的交换机：
        - ![[60-Link-layer-and-LAN-switcher.png]]

>[! note] MAC 如何确保不冲突？
> The answer is that the ==IEEE manages the MAC address space==.
> In particular, when a company wants to manufacture adapters, it purchases a chunk of the address space consisting of $2^{24}$ addresses for a nominal fee. IEEE allocates the chunk of $2^{24}$ addresses by fixing the first 24 bits of a MAC address and letting the company create unique combinations of the last 24 bits for each adapter.

- 网络地址和 MAC 地址分离
	- IP 地址和 MAC 地址的结构不同
		1) IP 地址是分层的
			- 一个子网所有站点网络号一致，路由聚集，减少路由表的长度
			- 需要一个网络中的站点地址网络号一致，如果捆绑需要定制网卡非常麻烦
			- 希望网络层地址是动态配置的，随着主机移动（局域网的网段）变化而变化
			- IP 地址完成网络到网络的交付
		2) MAC 地址是扁平的的
			- 网卡在生产时不知道被用于哪个网络，因此给网卡一个唯一的标示，用于区分一个网络内部不同的网卡即可
			- 可以完成一个物理网络内部的节点到节点的数据交付网络地址和 MAC 地址分离
	- 分离好处：
		1) 网卡坏了，IP 不变，可以捆绑到另外一个 MAC 的网卡上
		2) 物理网络还可以除 IP 之外支持其他网络层协议，链路协议为任意上层网络协议，如 IPX 等
	- 捆绑的问题
		1) 如果仅仅使用 IP 地址，不用 MAC 地址，那么它仅支持 IP 协议
		2) 每次上电都要重新写入网卡 IP 地址；
		3) 另外一个选择就是不使用任何地址；不用MAC地址，则每到来一个帧都要上传到IP层次，由它判断是不是需要接受，干扰一次

- 帧的发送与广播
	- When an adapter wants to send a frame to some destination adapter, ==the sending adapter inserts the destination adapter’s MAC address into the frame and then sends the frame into the LAN==. Thus, when an adapter receives a frame, it will check to see whether the destination MAC address in the frame matches its own MAC address. If there is a match, the adapter extracts the enclosed datagram and passes the datagram up the protocol stack. If there isn’t a match, the adapter discards the frame, without passing the network-layer datagram up. Thus, the destination only will be interrupted when the frame is received.（要发送给指定目的适配器时，会将目的适配器的 MAC 地址插入帧中再发送到 LAN。当适配器收到帧时，检查其中的 MAC 地址是否匹配，若匹配则提取帧中的数据并上交给网络层；若不匹配，则丢弃。）
	- However, sometimes a sending adapter does want all the other adapters on the LAN to receive and process the frame it is about to send. In this case, the sending adapter inserts a special MAC broadcast address into the destination address field of the frame. For LANs that use 6-byte addresses (such as Ethernet and 802.11), the broadcast address is a string of 48 consecutive 1s (that is, FF-FF-FF-FF-FF-FF in hexadecimal notation).（若将插入的 MAC 地址设置为 FF-FF-FF-FF-FF-FF，则意味着在LAN中广播该帧）

- 地址解析协议：Address Resolution Protocol
	- 提供 IP 地址和 MAC 地址之间的转换：
	- 在同一个 LAN 的例子：
		- ![[60-Link-layer-and-LAN-ARP-instra-LAN.png]]
		- 假设 C 向 A 发送 IP 数据报，则源主机需要向其适配器提供 IP 数据报和目的主机的 MAC 地址，然后由适配器构造成一个包含目的地 MAC 地址的链路层帧，再发送到 LAN 中；
		- ARP 的作用是，将相同 LAN 上的任何 IP 作为输入，返回相应的 MAC 地址——类似 DNS，但 DNS 支持的主机范围在全世界，而 ARP 只支持当前 LAN；
		- ARP 解析地址的工具是 ARP 表，在 LAN 上的每个 IP 节点都有一个 ARP 表
			- ARP 表：包括一些 LAN 节点 IP/MAC 地址的映射 —— `<IP address; MAC address; TTL>`
			- ![[60-Link-layer-and-LAN-ARP-table.png]]
			- TTL 时间是指地址映射失效的时间，典型是 20min。20min 内直接使用缓存 —— 高效；20min 后删除 —— 保持最新；
		- 如果 ARP 表中没有目的主机的表项：
			- 发送方构造一个 ARP 分组，其中包括发送方和接收方的 IP 地址和 MAC 地址，广播之，从而每个适配器都受到该分组，并将其传递给 ARP 模块，
			- ARP 模块检查后确定是否与其中的目的地址匹配，若匹配则在自己的 ARP 表中添加源主机的映射，并且单播回送一个正确的 ARP 响应分组，从而让源主机也正确地建立 ARP 映射；
		- 节点在自己的 ARP 表中，缓存 IP-to-MAC 地址映射关系，直到信息超时
			- 软状态：靠定期刷新维持的系统状态
			- 定期刷新周期之间维护的状态信息可能和原有系统不一致
		- ARP 是即插即用的，即节点自己创建和管理 ARP 的表项，无需网络管理员的干预。
	- 跨越路由器发送到不同 LAN 的例子：
		- ![[60-Link-layer-and-LAN-ARP-inter-LAN.png]]
		- 每台主机仅有一个 IP 地址和一个适配器（即一个 MAC 地址），而路由器对每个端口都有一个 IP 地址，因此路由器中对每个端口都有一个 ARP 模块和适配器；
		- 子网由掩码划分，其中子网 1 的前缀是 111.111.111，而子网 2 的前缀是 222.222.222。因此考查从子网 1 的一台主机向子网 2 的一台主机发送数据报：
			- In order for a datagram to go from 111.111.111.111 to a host on Subnet 2, the datagram must first be sent to the router interface 111.111.111.110, which is the IP address of the first-hop router on the path to the final destination. Thus, the appropriate MAC address for the frame is the address of the adapter for router interface 111.111.111.110, namely, E6-E9-00-17- BB-4B. How does the sending host acquire the MAC address for 111.111.111.110? By using ARP, of course!（IP 为111.111.111.111的主机向子网 2发送一个数据报，则应当首先发送到 IP 为111.111.111.110的路由器端口，因此正确的 MAC 地址也应当是该路由器端口的适配器的 MAC 地址。而这个 MAC 地址属于子网 1内部，因此通过 ARP 协议获取）
			- Once the sending adapter has this MAC address, it creates a frame (containing the datagram addressed to 222.222.222.222) and sends the frame into Subnet 1. The router adapter on Subnet 1 sees that the link-layer frame is addressed to it, and therefore passes the frame to the network layer of the router. Hooray—the IP datagram has successfully been moved from source host to the router!
			- But we are not finished. We still have to move the datagram from the router to the destination. The router now has to determine the correct interface on which the datagram is to be forwarded. As discussed in Chapter 4, this is done by consulting a forwarding table in the router. The forwarding table tells the router that the datagram is to be forwarded via router interface 222.222.222.220. This interface then passes the datagram to its adapter, which encapsulates the datagram in a new frame and sends the frame into Subnet 2. （在路由器内部如何判断正确的转发接口？路由表！因此该数据报（此时已经被脱去链路层附加的头部）被查表后转发到 IP 为222.222.222.220的接口，然后重新封装新的链路层帧头（因为目的MAC地址发生了改变））
			- This time, the destination MAC address of the frame is indeed the MAC address of the ultimate destination. And how does the router obtain this destination MAC address? From ARP, of course!

### Ethernet

以太网
- 目前最主流的LAN技术：98%占有率
- 廉价：30元RMB 100Mbps！
- 最早广泛应用的LAN技术
- 比令牌网和ATM网络简单、廉价
- 带宽不断提升：10M, 100M, 1G, 10G

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003093457375.png" style="zoom:60%"/>

以太网：物理拓扑
- 总线：在上个世纪90年代中期很流行
    - 所有节点在一个碰撞域内，一次只允许一个节点发送
    - 可靠性差，如果介质破损（总线长，破损概率大），截面形成信号的反射，发送节点误认为是冲突，总是冲突 —— 需要中继器吸收电磁能量
- 星型：目前最主流
    - 连接选择：hub 或者 switch 
    - 现在一般是交换机在中心
    - 每个节点以及相连的交换机端口使用（独立的）以太网协议（不会和其他节点的发送产生碰撞）

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003093613325.png" style="zoom:60%" />

以太帧结构
- 发送方适配器在以太网帧中封装IP数据报，或其他网络层协议数据单元

|preamble|destination address|source address|type|data(payload)|CRC|
|:---:|:---:|:---:|:---:|:---:|:---:|

- 前导码：
- 7B 10101010 + 1B 10101011
- 用来同步接收方和发送方的时钟速率
    - 使得接收方将自己的时钟调到发送端的时钟
    - 从而可以按照发送端的时钟来接收所发送的帧
- 地址：6字节源MAC地址，目标MAC地址
    - 如：帧目标地址=本站MAC地址，或是广播地址，接收，递交帧中的数据到网络层
    - 否则，适配器忽略该帧
- 类型：指出高层协（大多情况下是IP，但也支持其它网络层协议Novell IPX和AppleTalk）
- CRC：在接收方校验
    - 如果没有通过校验，丢弃错误帧

以太网：无连接、不可靠的服务（有形介质，出错概率较低）
- 无连接：帧传输前，发送方和接收方之间没有握手
- 不可靠：接收方适配器不发送ACKs或NAKs给发送方
    - 递交给网络层的数据报流可能有gap
    - 如上层使用像传输层TCP协议这样的rdt，gap会被补上（源主机，TCP实体）
    - 否则，应用层就会看到gap
- 以太网的MAC协议：采用二进制退避的CSMA/CD介质访问控制形式

802.3 以太网标准：链路和物理层
- 很多不同的以太网标准
    - 相同的MAC协议（介质访问控制）和帧结构
    - 不同的速率：2Mbps、10Mbps 、100Mbps 、1Gbps、10Gbps
    - 不同的物理层标准
    - 不同的物理层媒介：光纤，同轴电缆和双绞线

以太网使用CSMA/CD
- 没有时隙
- NIC如果侦听到其它NIC在发送就不发送：载波侦听(carrier sense)
- 发送时，适配器当侦听到其它适配器在发送就放弃对当前帧的发送，冲突检测(collision detection)
- 冲突后尝试重传，重传前适配器等待一个随机时间，随机访问(random access)

*具体看上面的CSMA/CD部分*

以太网在低负载和高负载的情况下都较好。低负载时好是由于CDMA/CD，高负载时好是由于引入了交换机，端口可以并发

10BaseT and 100BaseT
- 100Mbps速率也被称之为“fast ethernet”
- T代表双绞线
- 节点连接到HUB上：“star topology”物理上星型
- 逻辑上总线型，盒中总线
- 节点和HUB间的最大距离是100m

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003095952932.png" style="zoom:80%"/>

Hubs
- Hubs本质上是物理层的中继器：
    - 从一个端口收，转发到所有其他端口
    - 速率一致
    - 没有帧的缓存
    - 在hub端口上没有CSMA/CD机制：适配器检测冲突
    - 提供网络管理功能

Manchester编码 —— 物理层
- 在10BaseT中使用
- 每一个bit的位时中间有一个信号跳变，传送1时信号周期的中间有一个向下的跳变，传送0时信号周期的中间有一个向上的跳变（为什么要跳变？为了在电磁波信号中将时钟信号通过一些简单的电路抽取出来）
- 允许在接收方和发送方节点之间进行时钟同步
    - 节点间不需要集中的和全局的时钟
- 10Mbps，使用20M带宽，效率50%
- Hey, this is physical-layer stuff!

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003100140814.png" style="zoom:60%"/>

100BaseT中的4b5b编码（5个bit代表4个bit）

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003102620272.png" style="zoom:60%"/>

千兆以太网
- 采用标准的以太帧格式
- 允许点对点链路和共享广播信道
- 物理编码：8b10b编码
- 在共享模式，继续使用CSMA/CD MAC技术，节点间需要较短距离以提高利用率
- 交换模式：全双工千兆可用于点对点链路
    - 站点使用专用信道，基本不会冲突，效率高
    - 除非发往同一个目标站点
- 10Gbps now!

### switches 链路层交换机

Hub：集线器 （星形）
- 网段(LAN segments)：可以允许一个站点发送的网络范围
    - 在一个碰撞域，同时只允许一个站点在发送（发之前先侦听，做信道检测）
    - 如果有2个节点同时发送，则会碰撞
    - 通常拥有相同的前缀，比IP子网更详细的前缀
- HUB可以级联，所有以hub连到一起的站点处在一个网段/碰撞域（一个碰撞域内一次只能有一个节点发送，两个站点同步发送会导致碰撞，会采用CSMA/CD的方式尝试再次重发）
    - 骨干hub将所有网段连到了一起
- 通过hub可扩展节点之间的最大距离
- 通过HUB，不能将10BaseT和100BaseT的网络连接到一起

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003102803772.png" style="zoom:60%"/>

高负载情况下，由于CSMA/CD的限制，一般选择将HUB升级为交换机。（HUB“一发全收”，交换机只会存储帧并转发给确定的端口“选择性转发”，并行性强）

交换机
- 链路层设备：扮演主动角色（端口执行以太网协议）
    - 对帧进行存储和转发
    - 对于到来的帧，检查帧头，根据目标MAC地址进行选择性转发（而HUB是“一发全收”）
    - 当帧需要向某个（些）网段进行转发，需要使用CSMA/CD进行接入控制
    - 通常一个交换机端口一个独立网段，允许多个节点同时发送，并发性强
    - 交换机也可以级联，多级结构中通过自学习连接源站点和目标站点
- 透明：主机对交换机的存在可以不关心
    - 通过交换机相联的各节点好像这些站点是直接相联的一样（因为交换机处于链路层，路由节点/主机处于网络层）
    - 有MAC地址；无IP地址
- 即插即用，自学习(self learning)：
    - 交换机无需配置

交换机：多路同时传输
- 主机有一个专用和直接到交换机的连接
- 交换机缓存到来的帧
- 对每个帧进入的链路使用以太网协议，没有碰撞；全双工
    - 每条链路都是一个独立的碰撞域
    - MAC协议在其中的作用弱化了，基本上就是一个交换设备
- 交换：A-to-A' 和 B-to-B' 可以同时传输，没有碰撞

交换机转发表（转发表是自学习的，不用网络管理员配置）
- Q：交换机如何知道通过接口1到达A，通过接口5到达B'？
    - A：每个交换机都有一个交换表 switch table，每个表项：
        - （主机的MAC地址，到达该MAC经过的接口，时戳）
        - 比较像路由表！
- Q：每个表项是如何创建的？如何维护的？
    - 有点像路由协议？

交换机：自学习
- 交换机通过学习得到哪些主机（mac地址）可以通过哪些端口到达
- 当接收到帧，交换机学习到发送站点所在的端口（网段）
- 在交换表中记录发送方MAC地址/进入端口映射关系（软状态维护：通过时戳，隔一段时间就删掉这个表项）

    <img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003104914637.png" style="zoom:60%"/>

交换机：过滤／转发
- 当交换机收到一个帧：
    1. 记录进入链路，发送主机的MAC地址
    2. 使用目标MAC地址对交换表进行索引
    3. ```
       if entry found for destination{   // 选择性转发
           if dest on segment from which frame arrived{
               drop the frame // 过滤
           }
           else forward the frame on interface indicated // 转发
       }
       else flood // 泛洪：除了帧到达的网段，向所有网络接口发送
       ```

> 自学习，转发的例子：
> - 帧的目标：A'，不知道其位置在哪：泛洪
> - 知道目标A对应的链路：选择性发送到那个端口
> 
> <img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003105023894.png" width=300/>
> <br>
> <img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003105040787.png" width=300/>

交换机 vs. 路由器
- 都是存储转发设备，但层次不同
    - 交换机：链路层设备（检查链路层头部）
    - 路由器：网络层设备（检查网络层的头部）
- 都有转发表：
    - 交换机：维护交换表，按照MAC地址转发
        - 执行过滤、自学习和生成树算法
        - 即插即用；二层设备，速率高
        - 执行生成树算法，限制广播帧的转发
        - ARP表项随着站点数量增多而增多
    - 路由器：路由器维护路由表，执行路由算法
        - 路由算法能够避免环路，无需执行生成树算法，可以以各种拓扑构建网络
        - 对广播分组做限制
        - 不是即插即用的，配置网络地址（子网前缀）
        - 三层设备，速率低

## 6.5 链路虚拟化：MPLS

MPLS：多协议标记交换。按照标签label来交换分组，而非按照目标IP查询路由表进行存储转发，效率更高

## 6.6 数据中心网络

数万-数十万台主机构成DC网络

在交换机之间，机器阵列之间有丰富的互连措施:
- 在阵列之间增加吞吐（多个可能的路由路径）
- 通过冗余度增加可靠性

## 6.7 a day in the life of web request

回顾：页面请求的历程（以一个web页面请求的例子：综述！）
- 目标：标示、回顾和理解涉及到的协议（所有层次），以一个看似简单的场景：请求www页面
- 场景：学生在校园启动一台笔记本电脑：请求和接受 www.google.com

日常场景

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003110344335.png" style="zoom:90%"/>

1. 连接到互联网
- 笔记本需要一个IP地址，第一跳路由器的IP地址，DNS的地址：采用DHCP
- DHCP请求被封装在UDP中，进而封装在IP，进而封装在802.3以太网帧中
- 以太网的帧在LAN上广播(destination: FFFFFFFFFFFF)，被运行中的DHCP服务器接收到
- 以太网帧中解封装IP分组，解封装UDP，解封装DHCP
- DHCP 服务器生成DHCP ACK 包括客户端IP地址，第一跳路由器IP地址和DNS名字服务器地址
- 在DHCP服务器封装，帧通过LAN转发（交换机学习）在客户端段解封装
- 客户端接收DHCP ACK应答
- 此时：客户端便有了IP地址，知道了DNS域名服务器的名字和IP地址第一跳路由器的IP地址

2. ARP（DNS之前，HTTP之前）
- 在发送HTTP request请求之前，需要知道 www.google.com 的IP地址：DNS
- DNS查询被创建，封装在UDP段中，封装在IP数据报中，封装在以太网的帧中。将帧传递给路由器，但是需要知道路由器的接口：MAC地址：ARP
- ARP查询广播，被路由器接收，路由器用ARP应答，给出其IP地址某个端口的MAC地址
- 客户端现在知道第一跳路由器MAC地址，所以可以发送DNS查询帧了

3. 使用DNS
- 包含了DNS查询的IP数据报通过LAN交换机转发，从客户端到第一跳路由器
- IP数据报被转发，从校园到达comcast网络，路由（路由表被RIP，OSPF，IS-IS 和/或 BGP协议创建）到DNS服务器
- 被DNS服务器解封装
- DNS服务器回复给客户端： www.google.com 的IP地址

4. TCP连接携带HTTP报文
- 为了发送HTTP请求，客户端打开到达web服务器的TCP sockect
- TCP SYN 段（3次握手的第1次握手）域间路由到web服务
- web服务器用TCP SYNACK应答（3次握手的第2次握手）
- 客户端再次进行ACK确认的发送（3次握手的第3次握手）
- TCP连接建立了！

5. HTTP请求和应答
- HTTP请求发送到TCP socket中
- IP数据报包含HTTP请求，最终路由到 www.google.com
- IP数据报包含HTTP应答最后被路由到客户端
- web页面最后显示出来了！

## 6.8 总结

- 数据链路层服务背后的原理:
    - 检错、纠错
    - 共享广播式信道：多路访问
    - 链路编址
- 各种链路层技术的实例和实现
    - Ethernet
    - 交换式LANS，VLANs
    - 虚拟成链路层的网络：MPLS
- 综合：一个web页面请求的日常场景
