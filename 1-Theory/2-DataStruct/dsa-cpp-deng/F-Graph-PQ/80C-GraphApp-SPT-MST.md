## 优先级搜索
各种遍历算法的区别，仅在于选取顶点进行访问的次序：
- 广度/深度：优先访问与更早/更晚被发现的顶点相邻接者；
- 不同的遍历算法，取决于顶点的选取策略，不同的顶点选取策略，取决于存放和提供顶点的数据结构

- PFS 的思想就是为每个顶点 v 维护一个优先级数 priority (v)，每次选取访问的节点依据于 priority:
	- 每个顶点都有初始优先级数，并可能随算法的推进而调整 
	- 通常的习惯是，优先级数越大/小，优先级越低/高, 特别地，priority (v) == INT_MAX，意味着 v 的优先级最低

由此 PFS 可以作为一个算法框架，容纳很多对图的不同遍历需求。

### 算法框架
```
template <typename Tv, typename Te> 
template <typename PU> //优先级搜索（全图）
void Graph<Tv, Te>::pfs( Rank s, PU prioUpdater ) { // s < n
	reset(); //全图复位
	for ( Rank v = s; v < s + n; v++ ) //从s起顺次检查所有顶点
	    if ( UNDISCOVERED == status( v % n ) ) //一旦遇到尚未发现者
	        PFS( v % n, prioUpdater ); //即从它出发启动一次PFS
} //如此可完整覆盖全图，且总体复杂度依然保持为O(n+e)

template <typename Tv, typename Te>
template <typename PU> //顶点类型、边类型、优先级更新器
void Graph<Tv, Te>::PFS( Rank v, PU prioUpdater ) { //优先级搜索（单个连通域）
	priority( v ) = 0;
	status( v ) = VISITED; //初始化，起点v加至PFS树中
	
	while ( 1 ) { //将下一顶点和边加至PFS树中
	    for ( Rank u = firstNbr( v ); - 1 != u; u = nextNbr( v, u ) ) //对v的每一个邻居u
	        prioUpdater( this, v, u ); //更新其优先级及其父亲
	    int shortest = INT_MAX;
	    for ( Rank u = 0; u < n; u++ ) //从尚未加入遍历树的顶点中，选出下一个优先级
	        if ( ( UNDISCOVERED == status( u ) ) && ( shortest > priority( u ) ) ) //最高的
	            { shortest = priority( u ), v = u; } //顶点v
	    if ( shortest == INT_MAX ) break; //直至所有顶点均已加入
	    status( v ) = VISITED; type( parent( v ), v ) = TREE; //将v加入遍历树
	}//while
} //通过定义具体的优先级更新策略prioUpdater，即可实现不同的算法功能
```

### 复杂度
- 执行时间主要消耗于内、外两重循环；其中两个内循环前、后并列
	- 前一内循环的累计执行时间：若采用邻接矩阵，为 O (n^2)；若采用邻接表，为 O (n+e)
	- 后一循环中，优先级更新的次数呈算术级数变化{ n, n - 1, ..., 2, 1 }，累计为 O (n^2) ,两项合计，为 O (n2) 

- 后面可以改进：若采用优先级队列，以上两项将分别是 O (elogn)和 O (nlogn) ，两项合计，为 O ((e+n) logn) 
	- 这是很大的改进——尽管对于稠密图而言，反而是倒退 //已有接近于 O (e + nlogn)的算法
	- 基于这个统一框架，可以解决一系列应用问题。

## Dijkstra 算法求最短路径
### 问题描述
在给定有向连通图 G 及其中顶点 u 和 v，如何找到从 u 到 v 的最短路径？

这个问题应用广泛：
- 旅游者：最经济的出行路线 
- 路由器：最快地将数据包传送到目标位置
- 路径规划：多边形区域内的自主机器人

按照图的类型，可以划分为无权图、带权有向图（目前讨论的权值为非负，实际上负权值可以给每个权都加一个正数，使其非负再做讨论，最后减去这个整数就好）：
- Dijkstra 于 1959 年给出算法 SSSP (==Single-Source== Shortest Path)：给定顶点 s，计算 s 到其余各个顶点的最短路径及长度；
- Floyd-Warshall 于 1962 年给出 APSP (==All-Pairs== Shortest Path)：找出每对顶点 v 和 u 之间的最短路径及长度。

### 最短路径树
对于两点间最短路径问题，可以确定：**任一最短路径的前缀，也必然是最短路径**。如下图，将<s, v>的最短路径记作 $\pi(v)$，则有：
- ![[80C-GraphApp-SPT-MST-shortest-path.png]]
- $u\in\pi(v)\quad\Longleftrightarrow\quad\pi(u)\subseteq\pi(v)$ 

另外，在以 Dijkstra 方法构建最短路径树之前，需要明确：
- 各边权重均为正，否则有可能出现总权重非正的环路，以致最短路径无从定义
- 有负权重的边时，即便所有环路总权重皆为正，Dijkstra 算法依然可能失效
- 任意两点之间，最短路径唯一，在不影响计算结果的前提下，总可通过适当扰动予以保证（习题 6-17）

最短路径树 SPT：是所有最短路径的并集，并且继承了树的性质——极小连通图，最大无环图：
- $T=T_{n-1}=\bigcup\limits_{0\le i<n}\pi(u_{i})$ 构成的一棵树
- ![[80C-GraphApp-MST-SPT-dijkstra.png]]
- ![[80C-GraphApp-SPT-MST-SPT-instance.png]]
### 算法
思路：减治，每一次遍历都减小一次搜索范围：
![[80C-GraphApp-SPT-MST-dijkstra-1.png]]

![[80C-GraphApp-SPT-MST-dijkstra-2.png]]

![[80C-GraphApp-SPT-MST-dijkstra-3.png]]
### 实例
![[80C-GraphApp-SPT-MST-dijkstra-instance-1.png]]

![[80C-GraphApp-SPT-MST-dijkstra-instance-2.png]]

### 实现
1. 在图 G 及其子图中，每一轮都为可在一条路径到达的节点标记优先级 priority，其值决定于已确定范围到目标点的距离；
2. 套用 PFS 框架，每一轮 SPT 的增长：$T_{k}\Rightarrow T_{k+1}$ ，
	1. 只需要选出优先级最高的**跨边 CROSS**及其对应顶点 $u_k$，
		- ![[80C-GraphApp-SPT-MST-dijkstra-cross.png]]
	2. 将其接入 $T_k$ 即可，随后再更新 $V \text{\\} V_{k+1}$ 中所有顶点的优先级；
3. 需要注意，优先级随后可能改变（降低）的顶点，必定与 $u_{k}$ 邻接；
4. 因此只需枚举 $u_k$ 的每一邻接顶点 $v$，并取 $priority(v)=min(priority(v),priority(u_{k})+||u_{k},v||)$ 

因此对应于 Dijkstra 的优先级更新器如下：
```
//针对Dijkstra算法的顶点优先级更新器
template <typename Tv, typename Te> struct DijkPU { 
	virtual void operator()( Graph<Tv, Te>* g, Rank v, Rank u ) {
	    if ( UNDISCOVERED == g->status( u ) ) //对于v每一尚未被发现的邻接顶点u，按Dijkstra策略
	        if ( g->priority( u ) > g->priority( v ) + g->weight( v, u ) ) { //做松弛
		        g->priority( u ) = g->priority( v ) + g->weight( v, u ); //更新优先级（数）
	            g->parent( u ) = v; //并同时更新父节点
	        }
	}
};
```

整体 Dijkstra 算法如下：
```
//最短路径Dijkstra算法：适用于一般的有向图
template <typename Tv, typename Te>
void Graph<Tv, Te>::dijkstra( Rank s ) { // s < n
   reset(); priority( s ) = 0;
   for ( Rank i = 0; i < n; i++ ) { //共需引入n个顶点和n-1条边
      status( s ) = VISITED;
      if ( -1 != parent( s ) ) type( parent( s ), s ) = TREE; //引入当前的s
      for ( Rank j = firstNbr( s ); - 1 != j; j = nextNbr( s, j ) ) //枚举s的所有邻居j
         if ( ( status( j ) == UNDISCOVERED ) && ( priority( j ) > priority( s ) + weight( s, j ) ) ) //对邻接顶点j做松弛
            { priority( j ) = priority( s ) + weight( s, j ); parent( j ) = s; } //与Prim算法唯一的不同之处
      int shortest = INT_MAX;
      for ( Rank j = 0; j < n; j++ ) //选出下一最近顶点
         if ( ( status( j ) == UNDISCOVERED ) && ( shortest > priority( j ) ) )
            { shortest = priority( j ); s = j; }
   }
} //对于无向连通图，假设每一条边表示为方向互逆、权重相等的一对边
```

## Prim 算法求最小支撑树
### MST 是什么
- 支撑：对连通网络 N=(V; E)的子图 T=(V; F)，所谓支撑指的是覆盖 N 中所有可达顶点。树即可做到——连通且无环，并且树边数|F|=|V|-1；

- 最小：总权重 $w (T)=\sum\limits_{e\in F}w(e)$ 达到最小
	- MST 的优势应用：众多优化问题的基本模型，为许多 NP 问题提供足够好的近似解

### MST != SPT
- MST：针对子图总距离最短
- SPT：针对所求点距离最短

![[80C-GraphApp-SPT-MST-MST!=SPT.png]]

在考虑 MST 之前，需要确定：
- 权值必须是正数？
	- 允许为零，有何影响？
	- 允许为负数呢？
- 支撑树虽然可以有很多种，但所含的边数必然是相同的：|V|-1
- 存在权重相同的边的图中，构造的 MST 可能有多种，为了消除歧义，可以考虑对节点的信息也加入权重考量中：
	- ![[80C-GraphApp-SPT-MST-composite-number.png]]
### 蛮力算法
- 枚举出 N 的所有支撑树，再逐个计算代价
- 但是包含 n 个顶点的图，可能有多少棵支撑树？
	- n=1  1
	- n=2  1
	- n=3  3
	- n=4  16
	- n=5  125...
- [Cayley]( http://en.wikipedia.org/wiki/Cayley 's_formula) 公式：完全图 $K_{n}$ 有 $n^{n-2}$ 棵支撑树

### 改进思路：极短跨边
![[80C-GraphApp-SPT-MST-exclude-longest-edge.png]]
- **排除环路中最长边**
- 观察，任何环路 C 上的最长边 f，都不会被 MST 采用，否则都会有一个更短的边 e 取代之：
	- 移除 f 后，MST 分裂为两棵树，将其视作一个割[^1]，
	- 则 C 上必有该割的另一跨边 e，既然|e|<|f|，那么只要用 e 替换 f，就会得到一棵总权重更小的支撑树；（Kruskal 算法的依据）

![[80C-GraphApp-SPT-MST-include-shortest-edge.png]]
- **容纳割的最短边**
- 设 (U; V\\U)是 N 的一个割，若 uv 是该割的一条极短跨边，则必存在一棵包含 uv 的 MST（Prim 算法的依据）
- 反证：假设 uv 未被任何 MST 采用，任取一棵 MST，将 uv 加入其中，于是 
	- 将出现唯一的回路，
	- 且该回路必经过 uv 以及至少另一跨边 st 
	- 接下来，摘除 st 后又恢复为一棵支撑树，且总权重不致增加 
	- 反之，任一 MST 都必然通过极短跨边联接每一割

由此，可以得到 Prim 算法的递增式构造方法：
1. 首先，任选：$T_{1}=(\{v_{1}\};\varnothing)$ 
2. 不断地将 $T_k$ 扩展为 $T_{k+1}$：$T_{k+1}=(V_{k+1};E_{k+1})=(V_{k}\cup\{v_{k+1}\};E_{k}\cup\{v_{k+1},u\})$ 
3. 由此前的分析，只需将 $(V_k; V\text{\\}V_k)$ 视作原图的一个割，则该割所有跨边中的极短者即是 $(v_{k+1},u)$ 

![[80C-GraphApp-SPT-MST-Prim.png]]

### 实例
![[80C-GraphApp-SPT-MST-prim-instance1.png]]
![[80C-GraphApp-SPT-MST-prim-instance2.png]]

### 正确性
- 设 Prim 算法依次选取了边{ e2, e3, ..., en }，其中每一条边 ek，的确都属于某棵 MST 
- 但在 MST 不唯一时，由此并不能确认，最终的 T 必是 MST（之一），此时由极短跨边构成的支撑树，未必就是一棵 MST
- 可行的证明：在不增加总权重的前提下，可以将任一 MST 转换为 T，每一 Tk 都是某棵 MST 的子树，1 <= k <= n ——《习题解析》，6-28题

![[80C-GraphApp-SPT-MST-prim-iscorrect?.png]]

### 实现
1. 在图 G 及其子图中，每一轮都为可在一条路径可达的节点标记优先级 priority，其值决定于已确定范围到目标点的距离；
2. 套用 PFS 框架，每一轮 SPT 的增长：$T_{k}\Rightarrow T_{k+1}$ ，
	1. 只需要选出优先级最高的**跨边 CROSS**及其对应顶点 $u_k$，
		- ![[80C-GraphApp-SPT-MST-prim-pfs.png]]
	2. 将其接入 $T_k$ 即可，随后再更新 $V \text{\\} V_{k+1}$ 中所有顶点的优先级；
3. 需要注意，优先级随后可能改变（降低）的顶点，必定与 $u_{k}$ 邻接；
4. 因此只需枚举 $u_k$ 的每一邻接顶点 $v$，并取 $priority(v)=min(priority(v),||u_{k},v||)$ 

因此对应于 Prim 的优先级更新器如下：
```
template <typename Tv, typename Te> struct PrimPU { //针对Prim算法的顶点优先级更新器
   virtual void operator()( Graph<Tv, Te>* g, Rank v, Rank u ) {
      if ( UNDISCOVERED == g->status( u ) ) //对于v每一尚未被发现的邻接顶点u
         if ( g->priority( u ) > g->weight( v, u ) ) { //按Prim策略做松弛
            g->priority( u ) = g->weight( v, u ); //更新优先级（数）
            g->parent( u ) = v; //更新父节点
         }
   }
};
```

整体 Prim 算法如下：
```
template <typename Tv, typename Te> //Prim算法：无向连通图，各边表示为方向互逆、权重相等的一对边
void Graph<Tv, Te>::prim( Rank s ) { // s < n
   reset(); priority ( s ) = 0;
   for ( Rank i = 0; i < n; i++ ) { //共需引入n个顶点和n-1条边
      status( s ) = VISITED;
      if ( -1 != parent( s ) ) type( parent( s ), s ) = TREE; //引入当前的s
      for ( Rank j = firstNbr( s ); - 1 != j; j = nextNbr( s, j ) ) //枚举s的所有邻居j
         if ( ( status( j ) == UNDISCOVERED ) && ( priority( j ) > weight( s, j ) ) ) //对邻接顶点j做松弛
            { priority( j ) = weight( s, j ); parent( j ) = s; } //与Dijkstra算法唯一的不同之处
      int shortest = INT_MAX;
      for ( Rank j = 0; j < n; j++ ) //选出下一极短跨边
         if ( ( status( j ) == UNDISCOVERED ) && ( shortest > priority( j ) ) )
            { shortest = priority( j ); s = j; }
   }
}
```

## Kruskal 算法求 MST
### 描述
回顾 Prim 算法：
- 最短边，迟早会被采用
- 次短边，亦是如此
- 再次短者，则未必，因为可能出现回路！

Kruskal：贪心原则 
- 根据代价，从小到大依次尝试各边，只要“安全”——次短即可，就加入该边
- 但是，每步局部最优 = 全局最优？确实，Kruskal 很幸运...

### Kruskal 算法：
1. 维护 N 的一个森林：F = (V; E') ⊆ N = (V; E) 
2. 初始化: 
	- $F = (V; \varnothing)$ 包含 n 棵树（各含 1 个顶点）和 0 条边 
	- 将所有边按照代价排序
3. 迭代，直到 F 成为1棵树 
	- 找到当前最廉价的边 e 
	- 若 e 的顶点来自 F 中不同的树，则 
		- 令 E' = E' ∪ {e}，然后 
		- 将 e 联接的 2 棵树合二为一
4. 整个过程共迭代 n-1次，选出 n-1条边
![[80C-GraphApp-SPT-MST-kruskal-algo.png]]
### 正确性
定理：Kruskal 引入的每条边都属于某棵 MST 
- 设：边 e = (u, v)的引入导致树 T 和 S 的合并
- 若：将 (T; V\\T)视作原网络 N 的割，则：e 当属该割的一条跨边
- 在确定应引入 e 之前 
	- 该割的所有跨边都经 Kruskal 考察 
	- 且只可能因不短于 e 而被淘汰
- 故：e 当属该割的一条极短跨边
- 与 Prim 同理，以上论述也不充分，为严格起见，仍需归纳证明：Kruskal 算法过程中不断生长的森林，总是某棵 MST 的子图。

![[80C-GraphApp-SPT-MST-kruskal-iscorrect.png]]
### 并查集

## Floyd-Warshall 算法求最短距离

[^1]: 去掉其中所有边能使一张网络流图不再连通（即分成两个子图）的**边集**称为图的割（英语：cut）