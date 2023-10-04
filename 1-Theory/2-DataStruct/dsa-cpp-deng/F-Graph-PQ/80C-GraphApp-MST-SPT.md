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

![[80C-GraphApp-MST-SPT-dijkstra.png]]
### 最短路径树
### 算法
### 实例
### 实现

## Prim 算法求最小支撑树
### MST 是什么

### MST != SPT

### 蛮力算法

### 极短跨边

### 实例

### 正确性

### 实现

## Kruskal 算法求 MST
### 描述
### 正确性
### 并查集

## Floyd-Warshall 算法求最短距离
