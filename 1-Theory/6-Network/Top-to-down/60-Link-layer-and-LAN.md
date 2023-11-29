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
		- 选择最佳的 $p$ 且 $N$ 趋向无穷大时，效率为 $\frac{1}{2e} = 17.5\%$ ，效率比时隙 ALOHA 更差了！

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
	- 改进：CSMA --> CSMA/CD（目前有线介质的局域网如“以太网”采用的方式

4. ***CSMA/CD***(冲突检测) —— “边说边听” 
- 载波侦听CSMA：和在CSMA中一样发送前侦听信道
- 没有传完一个帧就可以在短时间内检测到冲突
- 冲突发生时则传输终止，减少对信道的浪费
- 冲突检测CD技术，有线局域网中容易实现：
    - 检测信号强度，比较传输与接收到的信号是否相同
    - 通过周期的过零点检测
- 人类类比：礼貌的对话人

以太网CSMA/CD算法
1. 适配器获取数据报，创建帧
2. 发送前：侦听信道CS
    - 闲：开始传送帧
    - 忙：一直等到闲再发送
3. 发送过程中，冲突检测CD
    - 没有冲突：成功
    - 检测到冲突：放弃，之后尝试重发
4. 发送方适配器检测到冲突，除放弃外，还发送一个Jam信号，所有听到冲突的适配器也是如此
    - 强化冲突：让所有站点都知道冲突（如何实现：信号强度大，持续时间长） 强化冲突的原因：放弃的信号可能持续时间很短，其他节点可能会接收不到，认为没有发生冲突，导致接受失败
5. 如果放弃，适配器进入指数退避状态 *exponential backoff 二进制指数退避算法*
    - 在第m次失败后，适配器随机选择一个{ 0，1，2，……，2^m-1}中的K，等待K*512位时，然后转到步骤2
    - 此时若两个站点继续选择了同一个K，则在K*512位时两者又同时重发，又会产生冲突
    - 随着m的增大，成功的概率越来越大，但是平均等待时间会变长
    - 注：指数退避：
        - 目标：适配器试图适应当前负载（自适应算法），在一个变化的碰撞窗口中随机选择时间点尝试重发
            - 高负载（重传节点多）：重传窗口时间大，减少冲突，但等待时间长
            - 低负载（重传节点少）：使得各站点等待时间少，但冲突概率大

CSMA/CD效率
- $t_{prop}$ 为LAN上2个节点的最大传播延迟
- $t_{trans}$ 为传输最大帧的时间
- 则：     
    
    $$ efficiency = \frac{1}{1 + 5 * t_{prop} / t_{trans}} $$

- 效率变为 $1$ ：
    - 当 $t_{prop}$ 变成 $0$ 时
    - 当 $t_{trans}$ 变成无穷大时
- 比ALOHA更好的性能，而且简单，廉价，分布式！

5. 无线局域网 CSMA/CA

WLAN由于是无线形式，更加倾向于选择CSMA/CA

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003011006144.png" style="zoom:70%"/>

无线局域网中的 MAC：CSMA/CA —— 冲突控制
- 冲突： $2^+$ 站点（AP或者站点）在同一个时刻发送
- 802.11：CSMA —— 发送前侦听信道，实现事先避免冲突
    - 不会和其它节点正在进行的传输发生冲突
- 802.11：没有冲突检测！
    - 无法检测冲突：自身信号远远大于其他节点信号（无线情况下，电磁波信号成平方反比衰减）
    - 即使能CD：不冲突 $\neq$ 成功 （有 隐藏终端 的问题：A的电磁波信号到达B时，C与A的距离比B与A的距离远，故此时电磁波到达不了C，即检测不到冲突，但是B周边会有A、C的电磁波的叠加干扰），同时 冲突 $\neq$ 不成功
    - 目标：avoid collisions：CSMA/C(ollision)A(voidance)
        - 无法CD，一旦发送一股脑全部发送完毕，不CD
        - 为了避免无CD带来的信道利用率低的问题，事前进行冲突避免

无线局域网：CSMA/CA
- 发送方
    - 如果站点侦测到信道空闲持续DIFS长，则传输整个帧 (no CD)
    - 如果侦测到信道忙碌，那么 选择一个随机回退值，并在信道空闲时递减该值；如果信道忙碌，回退值不会变化；到数到0时（只生在信道闲时）发送整个帧。如果没有收到ACK，增加回退值，重复这段的整个过程
- 802.11 接收方
    - 如果帧正确，则在SIFS后发送ACK

（无线链路特性，需要每帧确认（有线网由于边发边确认，不需要接收ACK信息就可以知道是否发送成功）；例如：由于隐藏终端问题，在接收端可能形成干扰，接收方没有正确地收到。链路层可靠机制）

IEEE 802.11 MAC 协议: CSMA/CA
- 在count down时，侦听到了信道空闲为什么不发送，而要等到0时在发送？以下例子可以很好地说明这一点
    - 2个站点有数据帧需要发送，第三个节点正在发送
    - LAN CD：让2者听完第三个节点发完，立即发送
        - 冲突：放弃当前的发送，避免了信道的浪费于无用冲突帧的发送
        - 代价不昂贵
    - WLAN：CA
        - 无法CD，一旦发送就必须发完，如冲突信道浪费严重，代价高昂
        - 思想：尽量事先避免冲突，而不是在发生冲突时放弃然后重发
        - 听到发送的站点，分别选择随机值，回退到0发送
            - 不同的随机值，一个站点会胜利
            - 失败站点会冻结计数器，当胜利节点发完再发
- 无法完全避免冲突
    - 两个站点相互隐藏：
        - A,B 相互隐藏，C在传输
        - A,B选择了随机回退值
        - 一个节点如A胜利了，发送
        - 而B节点收不到，顺利count down到0 发送
        - A,B的发送在C附近形成了干扰
    - 选择了非常靠近的随机回退值：
        - A,B选择的值非常近
        - A到0后发送
        - 但是这个信号还没到达B时
        - B也到0了，发送
        - 冲突

冲突避免：RTS-CTS交换 —— 对长帧的可选项
- 思想：允许发送方“预约”信道，而不是随机访问该信道：避免长数据帧的冲突（可选项）
- 发送方首先使用CSMA向BS发送一个小的RTS分组
    - RTS可能会冲突（但是由于比较短，浪费信道较少）
- BS广播 clear-to-send CTS，作为RTS的响应
- CTS能够被所有涉及到的节点听到
    - 发送方发送数据帧
    - 其它节点抑制发送

采用小的预约分组，可以完全避免数据帧的冲突

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003011156385.png" style="zoom:70%"/>

b.6 线缆接入网络 —— 有线电视公司提供

- 多个40Mbps下行（广播）信道，FDM
    - 下行：通过FDM分成若干信道，互联网、数字电视等
    - 互联网信道：只有1个用户（不存在竞争） —— CMTS在其上传输，将数据往下放
- 多个30Mbps上行的信道，FDM
    - 多路访问：所有用户使用；接着TDM分成微时隙
    - 部分时隙：分配（预约）；部分时隙：竞争；

DOCSIS：data over cable service interface spec 
- 采用FDM进行信道的划分：若干上行、下行信道
- 下行信道：
    - 在下行MAP帧中：CMTS告诉各节点微时隙分配方案，分配给各站点的上行微时隙
    - 另外：头端传输下行数据（给各个用户）
- TDM上行信道
    - 采用TDM的方式将上行信道分成若干微时隙：MAP指定
    - 站点采用分配给它的微时隙上行数据传输：分配
    - 在特殊的上行微时隙中，各站点请求上行微时隙：竞争
        - 各站点对于该时隙的使用是随机访问的
        - 一旦碰撞（请求不成功，结果是：在下行的MAP中没有为它分配，则二进制退避）选择时隙上传输

#### 轮流协议

- 信道划分MAC协议：固定分配、平分
    - 共享信道在高负载时是有效和公平的
    - 在低负载时效率低下
        - 只能等到自己的时隙开始发送或者利用1/N的信道频率发送
        - 当只有一个节点有帧传时，也只能够得到1/N个带宽分配
- 随机访问MAC协议
    - 在低负载时效率高：单个节点可以完全利用信道全部带宽
    - 高负载时：冲突开销较大，效率极低，时间很多浪费在冲突中
- 轮流协议
    - 有2者的优点
    - 缺点：复杂

轮流MAC协议
- 轮询：（本身有一个主节点）
    - 主节点邀请从节点依次传送
    - 从节点一般比较“dumb”
    - 缺点：
        - 轮询开销：轮询本身消耗信道带宽
        - 等待时间：每个节点需等到主节点轮询后开始传输，即使只有一个节点，也需要等到轮询一周后才能够发送
        - 单点故障 <-- 本身可靠性差：主节点失效时造成整个系统无法工作
- 令牌传递：（没有主节点）
    - 控制令牌(token)循环从一个节点到下一个节点传递 （“击鼓传花”）。如果令牌到达的节点需要发送信息，就将令牌位置位为0，将令牌帧变为数据帧，发送的数据绕行一周后再由自己收下（为什么不是由目标节点收下？“一发多收”，目标节点有多个，如果由目标节点收下会导致后面的目标节点收不到，所以需要轮转一圈，确认经过了所有节点后由自己收下）
    - 令牌报文：特殊的帧
    - 缺点：
        - 令牌开销：本身消耗带宽
        - 延迟：只有等到抓住令牌，才可传输
        - 单点故障(token)：
            - 令牌丢失系统级故障，整个系统无法传输
            - 复杂机制重新生成令牌

MAC协议总结
- 多点接入问题：对于一个共享型介质，各个节点如何协调对它的访问和使用？
    - 信道划分：按时间、频率或者编码
        - TDMA、FDMA、CDMA
    - 随机访问（动态）
        - ALOHA, S-ALOHA, CSMA, CSMA/CD
        - 载波侦听：在有些介质上很容易（wire：有线介质），但在有些介质上比较困难（wireless：无线）
        - CSMA/CD：802.3 Ethernet网中使用
        - CSMA/CA：802.11WLAN中使用
    - 依次轮流协议
        - 集中：由一个中心节点轮询；分布：通过令牌控制
        - 蓝牙、FDDI、令牌环

## 6.4 LANS

### 6.4.1 addressing, ARP

- MAC地址和ARP
    - 32bit IP地址：
        - 网络层地址
        - 前n-1跳：用于使数据报到达目的IP子网 —— IP地址的网络号部分
        - 最后一跳：到达子网中的目标节点 —— IP地址的主机号部分
    - LAN（MAC/物理/以太网）地址：
        - 用于使帧从一个网卡传递到与其物理连接的另一个网卡（在同一个物理网络中）
        - 48bit MAC地址固化在适配器的ROM，有时也可以通过软件设定
        - 理论上全球任何2个网卡的MAC地址都不相同
        - e.g., 1A-2F-BB-76-09-AD <-- 16进制表示（每一位代表4个bits）

网络地址和mac地址分离
- IP地址和MAC地址的作用不同
    - a) IP地址是分层的
        - 一个子网所有站点网络号一致，路由聚集，减少路由表
            - 需要一个网络中的站点地址网络号一致，如果捆绑需要定制网卡非常麻烦
        - 希望网络层地址是配置的；IP地址完成网络到网络的交付
    - b) mac地址是一个平面的
        - 网卡在生产时不知道被用于哪个网络，因此给网卡一个唯一的标示，用于区分一个网络内部不同的网卡即可
        - 可以完成一个物理网络内部的节点到节点的数据交付网络地址和mac地址分离
- 分离好处
    - a) 网卡坏了，ip不变，可以捆绑到另外一个网卡的mac上
    - b) 物理网络还可以除IP之外支持其他网络层协议，链路协议为任意上层网络协议，如IPX等
- 捆绑的问题
    - a) 如果仅仅使用IP地址，不用mac地址，那么它仅支持IP协议
    - b) 每次上电都要重新写入网卡IP地址；
    - c) 另外一个选择就是不使用任何地址；不用MAC地址，则每到来一个帧都要上传到IP层次，由它判断是不是需要接受，干扰一次

> 假设网络上要将一个数据包（名为PAC）由北京的一台主机（名称为A，IP地址为IP_A，MAC地址为MAC_A）发送到华盛顿的一台主机（名称为B，IP地址为IP_B，MAC地址为MAC_B）。       
> 这两台主机之间不可能是直接连接起来的，因而数据包在传递时必然要经过许多中间节点（如路由器，服务器等等），我们假定在传输过程中要经过C1、C2、C3（其MAC地址分别为M1，M2，M3）三个节点。          
> A在将PAC发出之前，先发送一个ARP请求，找到其要到达IP_B所必须经历的第一个中间节点C1的MAC地址M1，然后在其数据包中封装（Encapsulation）这些地址：IP_A、IP_B，MAC_A和M1。当PAC传到C1后，再由ARP根据其目的IP地址IP_B，找到其要经历的第二个中间节点C2的MAC地址M2，然后再将带有M2的数据包传送到C2。如此类推，直到最后找到带有IP地址为IP_B的B主机的地址MAC_B，最终传送给主机B。

LAN地址和ARP
- 局域网上每个适配器都有一个唯一的LAN地址

<img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003091929111.png" style="zoom:80%"/>

- MAC地址由IEEE管理和分配
- 制造商购入MAC地址空间（保证唯一性）
- 类比：
    - (a)MAC地址：社会安全号、身份证号码
    - (b)IP地址：通讯地址、住址
- MAC平面地址 --> 支持移动
    - 可以将网卡到接到其它网络
- IP地址有层次 --> 不能移动
    - 依赖于节点连接的IP子网，与子网的网络号相同（有与其相连的子网相同的网络前缀）

ARP(Address Resolution Protocol)
- 问题：已知B的IP地址，如何确定B的MAC地址？ARP协议
    - 在LAN上的每个IP节点都有一个ARP表
    - ARP表：包括一些LAN节点IP/MAC地址的映射
        `<IP address; MAC address; TTL>`
        - TTL时间是指地址映射失效的时间，典型是20min。20min内直接使用缓存 —— 高效；20min后删除 —— 保持最新

ARP协议：在同一个LAN (网络)
- A要发送帧给B（B的IP地址已知），但B的MAC地址不在A的ARP表中
- A广播包含B的IP地址的ARP查询包
    - Destination MAC address = FF-FF-FF-FF-FF-FF
    - LAN上的所有节点都会收到该查询包
- B接收到ARP包，回复A自己的MAC地址
    - 帧发送给A
    - 用A的MAC地址（单播）
- A在自己的ARP表中，缓存IP-to-MAC地址映射关系，直到信息超时
    - 软状态：靠定期刷新维持的系统状态
    - 定期刷新周期之间维护的状态信息可能和原有系统不一致
- ARP是即插即用的
    - 节点自己创建ARP的表项
    - 无需网络管理员的干预、配置和操作

> 例：路由到其他LAN
> - Walkthrough：发送数据报：由A通过R到B，假设A知道B的IP地址
> - 在R上有两个ARP表，分别对应两个LAN
> - 在源主机的路由表中，发现到目标主机的下一跳时111.111.111.110
> - 在源主机的ARP表中，发现其MAC地址是E6-E9-00-17-BB-4B, etc
> 
> <img src="http://knight777.oss-cn-beijing.aliyuncs.com/img/image-20211003092755106.png" style="zoom:60%"/>
> 
> - 编址：
>     - A创建数据报，源IP地址：A；目标IP地址：B 
>     - A创建一个链路层的帧，目标MAC地址是R，该帧包含A到B的IP数据报
>     - 帧从A发送到R
>     - 帧被R接收到，从中提取出IP分组，交给上层IP协议实体
>     - R转发数据报，数据报源IP地址为A，目标IP地址为B
>     - R创建一个链路层的帧，目标MAC地址为B，帧中包含A到B的IP数据报

### 6.4.2 Ethernet

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

### 6.4.3 switches 链路层交换机

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
