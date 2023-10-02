## 基本术语
G=(V; E):
- G: Graph
- V: Vertex, nums = |V|
- E: Edge,   nums = |E|

- 同一条边的两个顶点，彼此邻接（adjacency）
- 同一顶点自我邻接，构成自环（self-loop）
- 不含自环及重边，即为**简单图**（simple graph），而非简单（non-simple）图，暂不讨论
- 顶点与其所属的边，彼此关联（incidence）
- 顶点的度（degree/valency）：与同一顶点关联的边数

### 无向图、有向图
- 若邻接顶点 u 和 v 的次序无所谓 则 (u, v)为无向边（undirected edge）
- 所有边均无方向的图，即无向图（undigraph）
- 反之，有向图（digraph）中均为有向边（directed edge）, u、v 分别称作边 (u, v)的尾（tail）、头（head）
- 无向边、有向边并存的图，称作混合图（mixed graph）
- 有向图通用性更强，故本章主要针对有向图介绍相关结构及算法。

### 路径、环路
- 路径 $\pi$ = <v0, v1, ..., vk>
- 长度| $\pi$ | = k 
- 简单路径：vi != vj 除非 i = j 
- 环/环路：v0 = vk 
- 有向无环图（DAG）:
	- 欧拉环路：| $\pi$ | = |E| 各边恰好出现一次
	- 哈密尔顿环路：| $\pi$ | = |V| 各顶点恰好出现一次
	- 欧拉环路和哈密尔顿环路都不唯一

![[80A-Graph-kinds.png]]

### 支撑树、带权网络
- 图 G = (V; E)的子图 T = (V; F)若是树，即为其支撑树（spanning tree） 同一图的支撑树，通常并不唯一
- 各边 e 均有对应的权值 wt (e)，则为带权网络（weighted network）
- 同一网络的支撑树中，总权重最小者为**最小支撑树**（MST）

![[80A-Graph-spanning-tree.png]]

## 图的存储结构
### ADT
```
using VStatus = enum { UNDISCOVERED, DISCOVERED, VISITED }; //顶点状态
using EType = enum { UNDETERMINED, TREE, CROSS, FORWARD, BACKWARD }; //边在遍历树中所属的类型

template <typename Tv, typename Te> //顶点类型、边类型
class Graph { //图Graph模板类
private:
	void reset() { //所有顶点、边的辅助信息复位
	    for ( Rank v = 0; v < n; v++ ) { //所有顶点的
	        status( v ) = UNDISCOVERED; dTime( v ) = fTime( v ) = -1; //状态，时间标签
	        parent( v ) = -1; priority( v ) = INT_MAX; //（在遍历树中的）父节点，优先级数
	        for ( Rank u = 0; u < n; u++ ) //所有边的
	            if ( exists( v, u ) ) type( v, u ) = UNDETERMINED; //类型
	    }
	}
	void BFS( Rank, Rank& ); //（连通域）广度优先搜索算法
	void DFS( Rank, Rank& ); //（连通域）深度优先搜索算法
	void BCC( Rank, Rank&, Stack<Rank>& ); //（连通域）基于DFS的双连通分量分解算法
	bool TSort( Rank, Rank&, Stack<Tv>* ); //（连通域）基于DFS的拓扑排序算法
	template <typename PU> void PFS( Rank, PU ); //（连通域）优先级搜索框架
public:
// 顶点
   Rank n; //顶点总数
   virtual Rank insert( Tv const& ) = 0; //插入顶点，返回编号
   virtual Tv remove( Rank ) = 0; //删除顶点及其关联边，返回该顶点信息
   virtual Tv& vertex( Rank ) = 0; //顶点的数据（该顶点的确存在）
   virtual Rank inDegree( Rank ) = 0; //顶点的入度（该顶点的确存在）
   virtual Rank outDegree( Rank ) = 0; //顶点的出度（该顶点的确存在）
   virtual Rank firstNbr( Rank ) = 0; //顶点的首个邻接顶点
   virtual Rank nextNbr( Rank, Rank ) = 0; //顶点（相对当前邻居的）下一邻居
   virtual VStatus& status( Rank ) = 0; //顶点的状态
   virtual Rank& dTime( Rank ) = 0; //顶点的时间标签dTime
   virtual Rank& fTime( Rank ) = 0; //顶点的时间标签fTime
   virtual Rank& parent( Rank ) = 0; //顶点在遍历树中的父亲
   virtual int& priority( Rank ) = 0; //顶点在遍历树中的优先级数
// 边：这里约定，无向边均统一转化为方向互逆的一对有向边，从而将无向图视作有向图的特例
   Rank e; //边总数
   virtual bool exists( Rank, Rank ) = 0; //边(v, u)是否存在
   virtual void insert( Te const&, int, Rank, Rank ) = 0; //在两个顶点之间插入指定权重的边
   virtual Te remove( Rank, Rank ) = 0; //删除一对顶点之间的边，返回该边信息
   virtual EType& type( Rank, Rank ) = 0; //边的类型
   virtual Te& edge( Rank, Rank ) = 0; //边的数据（该边的确存在）
   virtual int& weight( Rank, Rank ) = 0; //边(v, u)的权重
// 算法
   void bfs( Rank ); //广度优先搜索算法
   void dfs( Rank ); //深度优先搜索算法
   void bcc( Rank ); //基于DFS的双连通分量分解算法
   Stack<Tv>* tSort( Rank ); //基于DFS的拓扑排序算法
   void prim( Rank ); //最小支撑树Prim算法
   void dijkstra( Rank ); //最短路径Dijkstra算法
   template <typename PU> void pfs( Rank, PU ); //优先级搜索框架
};
```

### 邻接矩阵与关联矩阵
Adjacency matrix: 记录顶点之间的邻接关系
- 矩阵元素与图中可能存在的边，一一对应
	- A (v, u)=1 当且仅当 顶点 v 与 u 之间存在一条边
	- A (v, u)=0 当且仅当 顶点 v 与 u 之间不存在边
- 只考察简单图，则对角线统一设置为 0
- 空间复杂度为 Θ(n^2)，与图中实际拥有的边数无关，空间利用率=2e/(n^2)
![[80A-Graph-adjacency-matrix.png]]

Incidence matrix: 记录顶点与边之间的连接关系
- 空间复杂度为 Θ(n * e)=O (n^3)
- 空间利用率=2e/ne=2/n
![[80A-Graph-incidence-matrix.png]]

![[80A-Graph-adjacency-matrix-instance.png]]
- 可以看到无向图的邻接矩阵中，有一半是冗余的。

#### 邻接矩阵的模板实现
```
#include "Vector/Vector.h" //引入向量
#include "Graph/Graph.h" //引入图ADT

template <typename Tv> struct Vertex { //顶点对象（为简化起见，并未严格封装）
	Tv data; int inDegree, outDegree; VStatus status; //数据、出入度数、状态
	Rank dTime, fTime; //时间标签
	Rank parent; int priority; //在遍历树中的父节点、优先级数
	Vertex( Tv const& d = (Tv)0 ) : //构造新顶点
	    data( d ), inDegree( 0 ), outDegree( 0 ), status( UNDISCOVERED ), dTime( -1 ),
	    fTime( -1 ), parent( -1 ), priority( INT_MAX ) {} //暂不考虑权重溢出
};

template <typename Te> struct Edge { //边对象（为简化起见，并未严格封装）
	Te data; int weight;
	EType type; //数据、权重、类型
	Edge( Te const& d, int w ) : data( d ), weight( w ), type( UNDETERMINED ) {} //构造
};

template <typename Tv, typename Te> //顶点类型、边类型
class GraphMatrix : public Graph<Tv, Te> { //基于向量，以邻接矩阵形式实现的图
private:
	Vector<Vertex<Tv>> V; //顶点集（向量）
	Vector<Vector<Edge<Te>*>> E; //边集（邻接矩阵）
public:
	GraphMatrix() { n = e = 0; } //构造
	~GraphMatrix() { //析构
	    for ( Rank v = 0; v < n; v++ ) //所有动态创建的
	        for ( Rank u = 0; u < n; u++ ) //边记录
		        delete E[v][u]; //逐条清除
	}
	...
}; //Graph
```

#### 邻接矩阵的静态操作
```
template <typename Tv, typename Te>
class GraphMatrix : public Graph<Tv, Te> {
private:
	...
public:
	...
/**********************
 * 顶点的基本操作：查询第v个顶点（0 <= v < n）
***********************/
	virtual Tv& vertex( Rank v ) { return V[v].data; } //数据
	virtual Rank inDegree( Rank v ) { return V[v].inDegree; } //入度
	virtual Rank outDegree( Rank v ) { return V[v].outDegree; } //出度
	virtual Rank firstNbr( Rank v ) { return nextNbr( v, n ); } //首个邻接顶点
	virtual Rank nextNbr( Rank v, Rank u ) //相对于顶点u的下一邻接顶点（改用邻接表效率更高）
	    { while ( ( -1 != --u ) && !exists( v, u ) ); return u; } //逆向线性试探
	virtual VStatus& status( Rank v ) { return V[v].status; } //状态
	virtual Rank& dTime( Rank v ) { return V[v].dTime; } //时间标签dTime
	virtual Rank& fTime( Rank v ) { return V[v].fTime; } //时间标签fTime
	virtual Rank& parent( Rank v ) { return V[v].parent; } //在遍历树中的父亲
	virtual int& priority( Rank v ) { return V[v].priority; } //在遍历树中的优先级数


/**********************
 * 边的基本操作：查询顶点v与u之间的联边（0 <= v, u < n且exists(v, u)）
***********************/
	virtual bool exists( Rank v, Rank u ) //边(v, u)是否存在
	    { return ( v < n ) && ( u < n ) && ( E[v][u] != NULL ); }
	virtual EType& type( Rank v, Rank u ) { return E[v][u]->type; } //边(v, u)的类型
	virtual Te& edge( Rank v, Rank u ) { return E[v][u]->data; } //边(v, u)的数据
	virtual int& weight( Rank v, Rank u ) { return E[v][u]->weight; } //边(v, u)的权重

};
```

![[80A-Graph-adjancency-matrix-neighbour.png]]

#### 邻接矩阵的动态操作
```
/**********************
 * 顶点的动态操作
***********************/
	virtual Rank insert( Tv const& vertex ) { //插入顶点，返回编号
	    for ( Rank u = 0; u < n; u++ ) E[u].insert( NULL ); n++; //各顶点预留一条潜在的关联边
	    E.insert( Vector<Edge<Te>*>( n, n, (Edge<Te>*)NULL ) ); //创建新顶点对应的边向量
	    return V.insert( Vertex<Tv>( vertex ) ); //顶点向量增加一个顶点
	}
	virtual Tv remove( Rank v ) { //删除第v个顶点及其关联边（0 <= v < n）
	    for ( Rank u = 0; u < n; u++ ) //所有
	        if ( exists( v, u ) ) //出边
	        { delete E[v][u]; V[u].inDegree--; e--; } //逐条删除
	    E.remove( v ); n--; //删除第v行
	    Tv vBak = vertex( v ); V.remove( v ); //删除顶点v
	    for ( Rank u = 0; u < n; u++ ) //所有
	        if ( Edge<Te>* x = E[u].remove( v ) ) //入边
            { delete x; V[u].outDegree--; e--; } //逐条删除
	    return vBak; //返回被删除顶点的信息
	}

/**********************
 * 边的动态操作
***********************/
	virtual void insert( Te const& edge, int w, Rank v, Rank u ) { //插入权重为w的边(v, u)
	    if ( exists( v, u ) ) return; //确保该边尚不存在
	    E[v][u] = new Edge<Te>( edge, w ); //创建新边
	    e++; V[v].outDegree++; V[u].inDegree++; //更新边计数与关联顶点的度数
	}
	virtual Te remove( Rank v, Rank u ) { //删除顶点v和u之间的联边（exists(v, u)）
	    Te eBak = edge( v, u ); delete E[v][u];
	    E[v][u] = NULL; //备份后删除边记录
	    e--; V[v].outDegree--; V[u].inDegree--; //更新边计数与关联顶点的度数
	    return eBak; //返回被删除边的信息
	}
```

![[80A-Graph-adjancency-matrix-edge-dynamic-opt.png]]

![[80A-Graph-adjancency-matrix-vertex-dynamic-opt.png]]

#### 邻接矩阵的性能分析
- 直观，易于理解和实现
- 适用范围广泛，尤其适用于稠密图（dense graph）
- 判断两点之间是否存在联边：O (1)
- 获取顶点的（出/入）度数：O (1)（任何动态操作都会更新一遍顶点的度）
- 添加、删除边后更新度数：O (1)
- 扩展性（scalability）：得益于 Vector 良好的控制策略，空间溢出等情况可被“透明地”处理

缺点：
- Θ(n^2)空间，与边数无关！
- 不妨考察一类特定的图——平面图（planar graph）：可嵌入于平面的图
- Euler's formula (1750)： v - e + f - c = 1, for any PG
- 平面图：e <= 3 * n - 6 = O (n) << n^2 此时，空间利用率 ≈ 1/n
- 稀疏图（sparse graph） 空间利用率同样很低，可采用压缩存储技术 (只存非零元素的坐标和权值)


### 邻接表
思路：为避免邻接矩阵的空间浪费，则将邻接矩阵的各行组织成为列表，只记录存在的边及其权值。

等效于每一顶点 v 对应于列表 $L_{v}=\{u|<v,u>\in E\}$ :
![[80A-Graph-adjacency-list.png]]

实例如下：
![[图06-06.以邻接表方式描述和实现图.png]]
- 4 顶点 5 条弧：邻接矩阵需要 16 个单元的二维矩阵，邻接表则需要 9 个单元和 4 个表头；
- **在稀疏图中的优势更明显**。

#### 空间复杂度
- 有向图：O (n+e)
- 无向图：O (n+2e)
	- 无向弧被重复存储，若要改进，只需要...（[[81-Adjacency-multilist|邻接多重表]]）
- 平面图：O (n+3n)

#### 时间复杂度
- 建立邻接表：O (n+e) //递增式构造，如何实现？
- 枚举所有以顶点 v 为尾的弧：O (1+deg (v)) //遍历 v 的邻接表
- 枚举（无向图中）顶点 v 的邻居：O (1+deg (v)) //遍历 v 的邻接表
- 枚举所有以顶点 v 为头的弧：O (n+e) //遍历所有邻接表
	- 可改进至 O (1+deg (v)) //办法是建立逆邻接表，空间复杂度需要增加...
- 计算顶点 v 的出度/入度：
	- 增加度数的记录域：O (n)的附加空间
	- 增加/删除弧时更新度数：O (1)时间，总体 O (e)时间
	- 每次查询需要：O (1) 时间

给定顶点 u 和 v，判断<u, v>是否属于 E：
- 有向图：搜索 u 的邻接表，时间复杂度为 O (deg (u)) = O (e)
- 无向图：搜索 u 或 v 的邻接表，O (max (deg (u), deg (v)))=O (e)
- 并行搜索：O (2 * min (deg (u), deg (v)))=O (e)
- 如何达到邻接矩阵的 O (1)?
	- 利用散列技术，使弧的判定达到 expected-O (1) //与邻接矩阵相同
	- 空间：O (n+e) //与邻接表相同

#### 邻接矩阵与邻接表的取舍原则
- 邻接矩阵适用的场合：
	- 经常检测边的存在
	- 经常做边的插入和删除操作
	- 图的规模固定
	- 稠密图

- 邻接表适用的场合：
	- 经常计算顶点的度数
	- 顶点数目不确定
	- 经常做遍历
	- 稀疏图

### 十字链表
- 十字链表 (orthogonal-list) **存储的对象是有向图**。同邻接表相同的是，图中每个顶点各自构成一个链表，为链表的首元结点。
- 同时，对于有向图中的弧来说，有弧头和弧尾。==一个顶点所有的弧头的数量即为该顶点的入度，弧尾的数量即为该顶点的出度==。
- 每个顶点构成的链表中，以该顶点作为弧头的弧单独构成一个链表，以该顶点作为弧尾的弧也单独构成一个链表，两个链表的表头都为该顶点构成的头结点。
- 这样，由每个顶点构建的链表按照一定的顺序存储在数组中，就构成了十字链表。  

#### 节点结构
所以，十字链表中由两种结点构成：顶点结点和弧结点。各自的结构构成如下图所示：  

![[80A-Graph-orthogonal-list-node.png]]

- 弧结点中， tailvex 和 headvex 分别存储的是弧尾和弧头对应的顶点在数组中的位置下标； hlink 和 tlink 为指针域，分别指向弧头相同的下一个弧和弧尾相同的下一个弧； info 为指针域，存储的是该弧具有的相关信息，例如权值等。
- 顶点结点中，data 域存储该顶点含有的数据； firstin 和 firstout 为两个指针域，分别指向以该顶点为弧头和弧尾的首个弧结点。  

#### 实例

![](http://data.biancheng.net/uploads/allimg/170905/2-1FZ51A135458.png)  

  
例如，使用十字链表存储有向图 5（A） ，构建的十字链表如图 （B） 所示,构建代码实现为：

```c
#define  MAX_VERTEX_NUM 20
#define  InfoType int//图中弧包含信息的数据类型
#define  VertexType int
typedef struct ArcBox{
    int tailvex,headvex;//弧尾、弧头对应顶点在数组中的位置下标
    struct ArcBox *hlik,*tlink;//分别指向弧头相同和弧尾相同的下一个弧
    InfoType *info;//存储弧相关信息的指针
}ArcBox;
typedef struct VexNode{
    VertexType data;//顶点的数据域
    ArcBox *firstin,*firstout;//指向以该顶点为弧头和弧尾的链表首个结点
}VexNode;
typedef struct {
    VexNode xlist[MAX_VERTEX_NUM];//存储顶点的一维数组
    int vexnum,arcnum;//记录图的顶点数和弧数
}OLGraph;
int LocateVex(OLGraph * G,VertexType v){
    int i=0;
    //遍历一维数组，找到变量v
    for (; i<G->vexnum; i++) {
        if (G->xlist[i].data==v) {
            break;
        }
    }
    //如果找不到，输出提示语句，返回 -1
    if (i>G->vexnum) {
        printf("no such vertex.\n");
        return -1;
    }
    return i;
}
//构建十字链表函数
void CreateDG(OLGraph *G){
    //输入有向图的顶点数和弧数
    scanf("%d,%d",&(G->vexnum),&(G->arcnum));
    //使用一维数组存储顶点数据，初始化指针域为NULL
    for (int i=0; i<G->vexnum; i++) {
        scanf("%d",&(G->xlist[i].data));
        G->xlist[i].firstin=NULL;
        G->xlist[i].firstout=NULL;
    }
    //构建十字链表
    for (int k=0;k<G->arcnum; k++) {
        int v1,v2;
        scanf("%d,%d",&v1,&v2);
        //确定v1、v2在数组中的位置下标
        int i=LocateVex(G, v1);
        int j=LocateVex(G, v2);
        //建立弧的结点
        ArcBox * p=(ArcBox*)malloc(sizeof(ArcBox));
        p->tailvex=i;
        p->headvex=j;
        //采用头插法插入新的p结点
        p->hlik=G->xlist[j].firstin;
        p->tlink=G->xlist[i].firstout;
        G->xlist[j].firstin=G->xlist[i].firstout=p;
    }
}
```

对于链表中的各个结点来说，由于表示的都是该顶点的出度或者入度，所以结点之间没有先后次序之分，程序中构建链表对于每个新初始化的结点采用头插法进行插入。

#### 十字链表计算顶点的度

采用十字链表表示的有向图，在计算某顶点的出度时，为 firstout 域链表中结点的个数；入度为 firstin 域链表中结点的个数。

### 邻接多重表
![[81-Adjacency-multilist]]

## 图的遍历
### 广度优先搜索
#### 思路
- 对顶点 s 进行广度优先搜索