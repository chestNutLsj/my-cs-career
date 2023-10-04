## 拓扑排序

### DAG
有向无环图：Directed Acyclic Graph
- ![[80B-Graph-application-DAG.png]]
- 应用：
	- 类派生和继承关系图中，是否存在循环定义 
	- 操作系统中相互等待的一组线程，如何调度 
	- 给定一组相互依赖的课程，设计可行的培养方案 
	- 给定一组相互依赖的知识点，设计可行的教学进度方案 
	- 项目工程图中，设计可串行施工的方案 
	- email 系统中，是否存在自动转发或回复的回路...

### 拓扑排序的要求
- 任给有向图 G（不一定是 DAG），尝试将所有顶点排成一个线性序列，使其次序须与原图相容（意即，每一顶点都不会通过边指向前驱顶点）
- ![[80B-Graph-application-toposort-demand.png]]
- 接口要求：
	- 若原图存在回路（即并非 DAG），检查并报告 
	- 否则，给出一个相容的线性序列

### DAG 中的偏序关系
- 每个 DAG 对应于一个偏序集，而拓扑排序对应于一个全序集，拓扑排序的过程就是构造一个与指定偏序集相容的全序集。

- 可以拓扑排序的有向图必定无环，反之亦然，DAG 才能做拓扑排序得到线性序列，而且拓扑排序的结果至少一种。

- 有限的偏序集一定会有极值元素，由此归纳证明可以得到拓扑排序的算法。

### 零入度
#### 思路
在任何 DAG 中，必有一个零入度的顶点 m，若该 DAG 除去 m 点可以得到一个拓扑排序 S={$u_{k1},u_{k2},...,u_{kn-1}$}，则 S'={$m,u_{k1},u_{k2},...,u_{kn-1}$}即为该 DAG 的拓扑排序。如此递归下去即可完全排序。

需要注意的是：
- DAG 的子图亦为 DAG
- 只要 m 不唯一，则拓扑排序的结果也不唯一

#### 策略（伪代码）
1. 将所有入度为 0 的顶点存入栈 S，取空队列 Q //O (n)

2. 从栈中输出所有零入度的顶点，最后得到的序列是顺序的零入度顶点：
```
while ( ! S.empty() ) { //O(n)
	Q.enqueue( v = S.pop() ); //栈顶v转入队列
	for each edge( v, u ) //v的邻接顶点u若入度仅为1
		if ( u.inDegree < 2 ) S.push( u ); //则入栈
	G = G \ { v }; //删除v及其关联边（邻接顶点入度减1）
} //总体O(n + e)
return |G| ? "NOT_A_DAG" ： Q; //残留的G空，当且仅当原图可拓扑排序
```

#### 实例
![[80B-Graph-application-non-indegree-topo.png]]

### 零出度
#### 思路
相对应地，可以从 DAG 的零出度顶点逆向出发，递归地输出所有 DAG 及其子图的零出度顶点。

#### 策略（描述）
- 基于 DFS，借助栈 S，对图 G 做 DFS，得到组成 DFS 森林的一系列 DFS 树
- 其间每当有顶点被标记为 VISITED，则将其压入 S，一旦发现有后向边，则报告“NOT_A_DAG”并退出 (出现了环，不再是 DAG) ^67e5d3
- DFS 结束后，顺序弹出 S 中的各个顶点

各节点按 fTime 逆序排列，即是（零出度）拓扑排序 //因为 ftime 代表对一个顶点的访问结束，其在当前子图中不再有出度。

整体算法的复杂度与 DFS 相当，也是 O (n+e)

#### 实例
![[80B-Graph-application-non-outdegree-topo.png]]

#### 代码
```
//基于DFS的拓扑排序算法（全图）
template <typename Tv, typename Te>
Stack<Tv>* Graph<Tv, Te>::tSort( Rank s ) { // assert: 0 <= s < n
	reset(); Rank clock = 0; //全图复位
	Stack<Tv>* S = new Stack<Tv>; //用栈记录排序顶点
	for ( Rank v = s; v < s + n; v++ ) //从s起顺次检查所有顶点
	    if ( UNDISCOVERED == status( v % n ) ) //一旦遇到尚未发现者
	        if ( !TSort( v, clock, S ) ) { //即从它出发启动一次TSort
		        while ( !S->empty() ) //任一连通域（亦即整图）非DAG
		            S->pop();
	            break; //则不必继续计算，故直接返回
	        }
	return S; //若输入为DAG，则S内各顶点自顶向底排序；否则（不存在拓扑排序），S空
} //如此可完整覆盖全图，且总体复杂度依然保持为O(n+e)


//基于DFS的拓扑排序算法（单连通图）
template <typename Tv, typename Te>
bool Graph<Tv, Te>::TSort( Rank v, Rank& clock, Stack<Tv>* S ) { // v < n
	dTime( v ) = ++clock; status( v ) = DISCOVERED; //发现顶点v
	for ( Rank u = firstNbr( v ); - 1 != u; u = nextNbr( v, u ) ) //枚举v的所有邻居u
	    switch ( status( u ) ) { //并视u的状态分别处理
	        case UNDISCOVERED :
		        parent( u ) = v; type( v, u ) = TREE;
		        if ( !TSort( u, clock, S ) ) //从顶点u处出发深入搜索
		            return false; //若u及其后代不能拓扑排序（则全图亦必如此），故返回并报告
	            break;
	        case DISCOVERED :
	            type( v, u ) = BACKWARD; //一旦发现后向边（非DAG），则
	            return false; //不必深入，故返回并报告
	        default : // VISITED (digraphs only)
		        type( v, u ) = ( dTime( v ) < dTime( u ) ) ? FORWARD : CROSS;
	            break;
	    }
	status( v ) = VISITED; S->push( vertex( v ) ); //顶点被标记为VISITED时，随即入栈
	return true; // v及其后代可以拓扑排序
}
```

## 双连通分量
### 判定
#### 定义
- 无向图的**关节点** (articulation point, cut-vertex)：其删除之后，原图的连通分量增多
	- ![[80B-Graph-application-articulation-point.png]]
- 无关节点的图，称作双（重）连通图 (bi-connectivity)：任何一个顶点被去除，图仍是连通的
	- ![[80B-Graph-application-bioconnected-graph.png]]
- 极大的双连通子图，称作双连通分量 (Bi-Connected Components, BCC)
	- ![[80B-Graph-application-biconnected-components.png]]

#### 暴力法
确定一个无向图的各 BCC，如果使用暴力法，则逐个拆除关节点：
- 对每一顶点 v，通过遍历检查 `G\{v}` 是否仍连通；
- 这一方法需要 O (n*(n+e)的时间，并且找出关节点后仍需遍历重新确定 BCC

#### 改进：利用 DFS
构造 DFS 树，根据 DFS 留下的标记，甄别是否是关节点：
- 叶节点绝不是关节点——删除后对全树（图）的连通性没有影响；
- 考查非叶节点，若其是关节点，则必须有如下条件：
	- 对根 r，其必须有至少 2 棵子树：
		- ![[80B-Graph-application-bcc-root.png]]
	- 对内部节点 v，其有某个孩子 u，u 的子树不能经由 BACKWARD 边连接到 v 的任何真祖先 a（即取掉 v 也能通过 BACKWARD 连回到a）：
		- ![[80B-Graph-application-bcc-internal-nodes.png]]
		- 此时{v}= $BCC (u)\cap BCC (parent (v))$ 
		- 记最高可达祖先 hca(v) ，表示 subtree (v)中节点经 BACKWARD 可达的最高祖先：
			- 由括号引理，可以知道 dTime 越小的祖先，辈分越高，其中 dTime=0 的祖先为根节点
			- 在 DFS 过程中，一旦发现<v, u>是 BACKWARD，则取 hca (v)=min (hac (v), dTime (u))
			- 当 DFS (u)完成并返回到 v 时，若有 hca (u)<dTime (v)，则取 hca (v)=min (hca (v), hca (u))
			- 否则即可断定 v 是关节点，且{v}+subtree (u)即为一个 BCC

### 代码
```
//基于DFS的BCC分解算法(全图)
template <typename Tv, typename Te>
void Graph<Tv, Te>::bcc( Rank s ) { 
	reset(); Rank clock = 0; Rank v = s; 
	Stack<Rank> S; //栈S用以记录已访问的顶点
	do
	    if ( UNDISCOVERED == status( v ) ) { //一旦发现未发现的顶点（新连通分量）
	        BCC( v, clock, S ); //即从该顶点出发启动一次BCC
	        S.pop(); //遍历返回后，弹出栈中最后一个顶点――当前连通域的起点
	    }
	while ( s != ( v = ( ++v % n ) ) );
}

#define hca(x) (fTime(x)) //利用此处闲置的fTime[]充当hca[]
template <typename Tv, typename Te> //顶点类型、边类型
void Graph<Tv, Te>::BCC( Rank v, Rank& clock, Stack<Rank>& S ) { // assert: 0 <= v < n
	hca( v ) = dTime( v ) = ++clock; 
	status( v ) = DISCOVERED; 
	S.push( v ); // v被发现并入栈
	
	for ( int u = firstNbr( v ); - 1 != u; u = nextNbr( v, u ) ) //枚举v的所有邻居u
	    switch ( status( u ) ) { //并视u的状态分别处理
	        case UNDISCOVERED:
	            parent( u ) = v; type( v, u ) = TREE;
	            BCC( u, clock, S ); //从顶点u处深入
	            if ( hca( u ) < dTime( v ) ) //遍历返回后，若发现u（通过后向边）可指向v的真祖先
		            hca( v ) = min( hca( v ), hca( u ) ); //则v亦必如此
	            else //否则，以v为关节点（u以下即是一个BCC，且其中顶点此时正集中于栈S的顶部）
		            while ( u != S.pop() ); //弹出当前BCC中（除v外）的所有节点，可视需要做进一步处理
	            break;
	        case DISCOVERED:
	            type( v, u ) = BACKWARD; //标记(v, u)，并按照“越小越高”的准则
			    if ( u != parent( v ) ) 
				    hca( v ) = min( hca( v ), dTime( u ) ); //更新hca[v],越小越高
	            break;
	        default: //VISITED (digraphs only)
	            type( v, u ) = ( dTime( v ) < dTime( u ) ) ? FORWARD : CROSS;
		        break;
	    }
	status( v ) = VISITED; //对v的访问结束
}
#undef hca
```

### 复杂度
- 运行时间与常规的 DFS 相同，也是 O (n + e) ，栈操作的复杂度也不过如此
- 除原图本身，还需一个容量为 O (e)的栈存放已访问的边
- 为支持递归，另需一个容量为 O (n)的运行栈

如何推广至有向图的强连通分量 （Strongly-connected component）？
- Kosaraju's algorithm 
- Tarjan's algorithm

### 实例
![[80B-Graph-application-bcc-instance1.png]]

![[80B-Graph-application-bcc-instance2.png]]

![[80B-Graph-application-bcc-instance3.png]]