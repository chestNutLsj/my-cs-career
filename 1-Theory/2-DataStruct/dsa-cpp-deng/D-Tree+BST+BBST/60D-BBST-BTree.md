## B-Tree
### 背景
- 存储器容量的增长速度 << 应用问题规模的增长速度
- 因此相对而言，存储器的容量甚至在不断减小
	- 典型数据库规模/内存容量：
	- 1990： 10MB /  2MB =    5
	- 2020:  10TB / 10GB = 1000
- 在特定工艺及成本下，存储器都是容量与速度折中的产物
- 实用的存储系统，由不同类型的存储器级联得到，而内存的访问速度与外存的访问速度的数量级差距之大，可达 10000 倍甚至更多。因此最大限度地利用数据访问的局部性，多多地访问内存。
- 策略：常用的数据，复制到更高层、更小的存储器中，找不到，才向更低层、更大的存储器索取；
- 此时==算法的实际运行时间，将取决于相邻存储级别之间数据 I/O 的速度与次数==；
- 并且外存是块设备，以页、段为块单位进行传送，读 1B 和读 1 块的用时基本相同，借助缓冲区，可以大大缩短单位字节的平均访问时间。
- 因此算法的主要优化目标是，如何提高缓冲策略的命中率。

### 特点
![[60D-BBST-BTree-collapse.png]]

- 每 d 代合并为超级节点，则除根节点外每个节点分 m=2^d 路，每个节点最多 m-1 个关键码；
- 思考在访问外存期间 BTree 的优势：
	- 若 n=1G 个记录，对 AVL 树而言查找需要 $\log_{2}10^{9}$ 次 I/O，每次只读单个关键码，代价太大；
	- 对 B-Tree，充分利用外存的批量访问，每下降一层，都以超级节点为单位读入一组关键码；
	- 具体多大视数据块的大小确定，m=keys_num / page，通常 m=200~300；
	- 以 m=256 为例，每次查找只需 $\log_{256}10^{9}<4$ 次 I/O

#### 准确定义
终于，对 BTree 作一个准确定义：
![[60D-BBST-BTree-nodes.png]]
- m 阶 BTree，即 m 路完全平衡搜索树，外部节点的深度统一相等，以该深度作 BTree 的树高h
- 注意==以此定义，根节点深度为 0，外部节点的高度为 0，叶节点高度为 1==；
- 叶节点的深度统一为 h-1，也相等；
- 内部节点
	- 最多含 m-1 个关键码，每个关键码以升序排列（有序就行），
	- 每个关键码对应小于自身一个左分支，最后一个关键码右侧有一个大于自身的右分支，即分支数最多 m 个
	- 树根的分支不能小于 2（即 1 个关键码），其它内部节点的分支不能小于 $\lceil \frac{m}{2}\rceil$，（即关键码不小于 $\lceil \frac{m}{2}\rceil-1$）
- 故 BTree 的准确名称为：$(\lceil \frac{m}{2}\rceil,m)$ - B 树

#### 举例
- m = 3：2-3-树，(2,3)-树 
	- 最简单的 B-树 //J. Hopcroft, 1970 
	- 各（内部）节点的分支数，可能是 2 或 3 
	- 各节点所含 key 的数目，可能是 1 或 2 

- m = 4：2-3-4-树，(2,4)-树 
	- 各节点的分支数，可能是 2、3 或 4 
	- 各节点所含 key 的数目，可能是 1、2 或 3 
	- ![[60D-BBST-BTree-2-4-BTree.png]]
	- 红黑树就是一种 (2,4)-树。

### ADT
```
// BTNode
template <typename T> 
struct BTNode { //B-树节点
	BTNodePosi<T> parent; //父
	Vector<T> key; //关键码
	Vector< BTNodePosi<T> > child; //孩子
	BTNode() { parent = NULL; child.insert( NULL ); }
	BTNode( T e, BTNodePosi<T> lc = NULL, BTNodePosi<T> rc = NULL ) {
		parent = NULL; //作为根节点
		key.insert( e ); //仅一个关键码，以及
		child.insert( lc ); 
		if ( lc ) lc->parent = this; //左孩子
		child.insert( rc ); 
		if ( rc ) rc->parent = this; //右孩子
	}
};
```

```
// BTree
template <typename T> using BTNodePosi = BTNode<T>*; //B-树节点位置
template <typename T> 
class BTree { //B-树
protected:
	Rank _size, _m; //关键码总数、阶次
	BTNodePosi<T> _root, _hot; //根、search()最后访问的非空节点
	void solveOverflow( BTNodePosi<T> ); //因插入而上溢后的分裂处理
	void solveUnderflow( BTNodePosi<T> ); //因删除而下溢后的合并处理

public:
	BTree( int m = 3 ) : _m( m ), _size( 0 ) {
		//构造函数：默认为最低的3阶
		_root = new BTNode<T>(); 
	}
	~BTree() { 
		//析构函数：释放所有节点
		if ( _root ) release( _root ); 
	} 
   int const order() { return _m; } //阶次
   int const size() { return _size; } //规模
   BTNodePosi<T> & root() { return _root; } //树根
   bool empty() const { return !_root; } //判空
   BTNodePosi<T> search ( const T& e ); //查找
   bool insert ( const T& e ); //插入
   bool remove ( const T& e ); //删除
};
```

### 查找
BTree 的根节点常驻 RAM 中，查找步骤如下：
- 只要当前节点不是外部节点
	- 在当前节点中顺序查找（二分亦可，但效率提升并不明显）
	- 若找到目标关键码，则
		- 返回查找成功
	- 否则
		- 沿引用向下搜索，在孩子节点中查找（I/O 耗时）
		- 若找到，则读入内存中
- 返回查找失败
![[60D-BBST-BTree-search.png]]

```
template <typename T> 
BTNodePosi<T> BTree<T>::search( const T & e ) {
	BTNodePosi<T> v = _root;
	_hot = NULL; //从根节点出发
	while ( v ) { //逐层深入地
		Rank r = v->key.search( e ); //在当前节点对应的向量中顺序查找
		if ( 0 <= r && e == v->key[r] ) return v; //若成功，则返回；否则...
		_hot = v; v = v->child[ r + 1 ]; //沿引用转至对应的下层子树，并载入其根（I/O）
	} //若因!v而退出，则意味着抵达外部节点
	return NULL; //失败
}
```

#### 性能
约定：
- 根节点常驻 RAM, 
- 内存中查找耗时忽略，
- 运行时间主要取决于 I/O 次数，
- 每个深度最多进行一次 I/O

故，
- search 需要的运行时间=O (logn) —— 以阶数 m 作底

#### 树高
- 树高 h：$\log_{m}(N+1)\le h\le 1+\lfloor\log_{\lceil \frac{m}{2}\rceil} \frac{N+1}{2}\rfloor$ 
	- 左边对应树高最低情况，即每个节点都有 m 路分支
	- 右边对应树高最高情况，即每个节点都有 $\lceil \frac{m}{2}\rceil$ 路分支
![[60D-BBST-BTree-height-maxist.png]]
![[60D-BBST-BTree-height-minist.png]]

### 插入
```
template <typename T> 
bool BTree<T>::insert( const T & e ) {
	BTNodePosi<T> v = search( e );
	if ( v ) return false; //确认e不存在
	Rank r = _hot->key.search( e ); //在节点_hot中确定插入位置
	_hot->key.insert( r+1, e ); //将新关键码插至对应的位置
	_hot->child.insert( r+2, NULL ); _size++; //创建一个空子树指针
	solveOverflow( _hot ); //若上溢，则分裂
	return true; //插入成功
}

```

#### 分裂
- 若上溢节点中关键码为 $\{k_0, k_1,..., k_{m-1}\}$ ，取中位数 $s=\lfloor \frac{m}{2}\rfloor$，则以关键码 $k_s$ 划分为：$\{k_0, k_1,..., k_{s-1}\}$ $\{k_s\}$ $\{k_{s+1},..., k_{m-1}\}$
- 关键码 k_s 上升一层，分裂所得到的两个（超级）节点作左右孩子
- 若上溢的节点导致父节点溢出，则继续会发生上溢分裂
- 上溢传播最坏情况不过也就是到根，若在根节点也导致上溢分裂，则树高增加 1；
- 总体执行时间正比于分裂次数 O (h)

![[71-BTree#Insert-Example]] 

![[60D-BBST-BTree-split-instance.png]]

#### 上溢修复
```
template <typename T> //关键码插入后若节点上溢，则做节点分裂处理
void BTree<T>::solveOverflow( BTNodePosi<T> v ) {
	while ( _m <= v->key.size() ) {//除非当前节点并未上溢
		//划分轴点
		//此时应有_m = key.size() = child.size() - 1
	    Rank s = _m / 2; 
	    BTNodePosi<T> u = new BTNode<T>(); //注意：新节点已有一个空孩子
	    for ( Rank j = 0; j < _m - s - 1; j++ ) { 
		    // v右侧_m-s-1个孩子及关键码分裂为右侧节点u
	        u->child.insert( j, v->child.remove( s + 1 ) ); //逐个移动效率低
	        u->key.insert( j, v->key.remove( s + 1 ) ); //此策略可改进
		}
		u->child[_m - s - 1] = v->child.remove( s + 1 ); //移动v最靠右的孩子
	    if ( u->child[0] ) //若u的孩子们非空，则
	        for ( Rank j = 0; j < _m - s; j++ ) //令它们的父节点统一
            u->child[j]->parent = u; //指向u
	    BTNodePosi<T> p = v->parent; // v当前的父节点p
	    if ( !p ) { 
		    _root = p = new BTNode<T>(); 
		    p->child[0] = v; 
		    v->parent = p; } //若p空则创建之
	    Rank r = 1 + p->key.search( v->key[0] ); // p中指向v的指针的秩
	    p->key.insert( r, v->key.remove( s ) ); //轴点关键码上升
	    p->child.insert( r + 1, u ); u->parent = p; //新节点u与父节点p互联
	    v = p; //上升一层，如有必要则继续分裂――至多O(logn)层
	} //while
}
```

### 删除
```
template <typename T> 
bool BTree<T>::remove( const T& e ) { 
	//从BTree树中删除关键码e
	BTNodePosi<T> v = search( e ); 
	if ( !v ) return false; //确认目标关键码存在
	Rank r = v->key.search( e ); //确定目标关键码在节点v中的秩（由上，肯定合法）
	if ( v->child[0] ) { //若v非叶子，则e的后继必属于某叶节点
	    BTNodePosi<T> u = v->child[r + 1]; //在右子树中一直向左，即可
	    while ( u->child[0] ) u = u->child[0]; //找出e的后继
	    v->key[r] = u->key[0]; v = u; r = 0; //并与之交换位置
	} //至此，v必然位于最底层，且其中第r个关键码就是待删除者
	v->key.remove( r ); 
	v->child.remove( r + 1 ); 
	_size--; //删除e，以及其下两个外部节点之一
	solveUnderflow( v ); //如有必要，需做旋转或合并
	return true;
}
```

非根节点 V 需要下溢时，恰有 $\lceil \frac{m}{2}\rceil -2$ 个关键码和 $\lceil \frac{m}{2}\rceil -1$ 个分支，视其兄弟 L 或 R 的规模，可分三种情况处理：
1. L 存在且至少包含 $\lceil \frac{m}{2}\rceil$ 个关键码
	- ![[60D-BBST-BTree-delete2.png]]
	- 则将 P 中的分解关键码 y 移至 V 中作为最小关键码
	- 将 L 中最大关键码 x 移至 P 中取代原关键码y
	- 如此旋转后，==局部乃至全树都重新满足 BTree 的条件==

2. R 存在且至少包含 $\lceil \frac{m}{2}\rceil$ 个关键码
	- 镜像地，旋转解决；

3. L 和 R 或不存在，或均不满足 $\lceil \frac{m}{2}\rceil$ 个关键码
	- 即便如此，L 和 R 仍至少有一者恰含 $\lceil \frac{m}{2}\rceil-1$ 个关键码
	- ![[60D-BBST-BTree-delete3.png]]
	- 从 P 中抽出介于 L 和 V 之间的分解关键码 y，通过 y 将 L 和 V 合成一个节点，同时合并此前 y 的孩子引用；
	- 若此处下溢得以修复，又可能导致 P 发生下溢，继而上升至根，但最多不过 O (h)层；

#### 举例
![[71-BTree#Case I -- delete leaf]]

![[71-BTree#Case II -- delete the internals]]

![[71-BTree#Case III - shrink height]]

#### 下溢修复
```
template <typename T> //关键码删除后若节点下溢，则做节点旋转或合并处理
void BTree<T>::solveUnderflow( BTNodePosi<T> v ) {
	while ( ( _m + 1 ) / 2 > v->child.size() ) { //除非当前节点并未下溢
	    BTNodePosi<T> p = v->parent;
	    if ( !p ) { //已到根节点（两个孩子即可）
	        if ( !v->key.size() && v->child[0] ) {
	            //但倘若作为树根的v已不含关键码，却有（唯一的）非空孩子，则
	            _root = v->child[0]; 
	            _root->parent = NULL; //这个节点可被跳过
	            v->child[0] = NULL; 
	            release( v ); //并因不再有用而被销毁
	        } //整树高度降低一层
	        return;
	    }
	    Rank r = 0;
	    while ( p->child[r] != v ) r++;//确定v是p的第r个孩子――此时v可能不含关键码，故不能通过关键码查找
	    //另外，在实现了孩子指针的判等器之后，也可直接调用Vector::find()定位

		// 情况1：向左兄弟借关键码
	    if ( 0 < r ) { //若v不是p的第一个孩子，则
	        BTNodePosi<T> ls = p->child[r - 1]; //左兄弟必存在
	        if ( ( _m + 1 ) / 2 < ls->child.size() ) { //若该兄弟足够“胖”，则
	            v->key.insert( 0, p->key[r - 1] ); // p借出一个关键码给v（作为最小关键码）
	            p->key[r - 1] = ls->key.remove( ls->key.size() - 1 ); // ls的最大关键码转入p
	            v->child.insert( 0, ls->child.remove( ls->child.size() - 1 ) );
	            //同时ls的最右侧孩子过继给v
	            if ( v->child[0] ) v->child[0]->parent = v; //作为v的最左侧孩子
            return; //至此，通过右旋已完成当前层（以及所有层）的下溢处理
	        }
	    } //至此，左兄弟要么为空，要么太“瘦”

		// 情况2：向右兄弟借关键码
	    if ( p->child.size() - 1 > r ) { //若v不是p的最后一个孩子，则
	        BTNodePosi<T> rs = p->child[r + 1]; //右兄弟必存在
	        if ( ( _m + 1 ) / 2 < rs->child.size() ) { //若该兄弟足够“胖”，则
	            v->key.insert( v->key.size(),p->key[r] ); // p借出一个关键码给v（作为最大关键码）
	            p->key[r] = rs->key.remove( 0 ); // rs的最小关键码转入p
	            v->child.insert( v->child.size(), rs->child.remove( 0 ) );
	            //同时rs的最左侧孩子过继给v
	            if ( v->child[v->child.size() - 1] ) //作为v的最右侧孩子
		            v->child[v->child.size() - 1]->parent = v;
	            return; //至此，通过左旋已完成当前层（以及所有层）的下溢处理
	        }
	    } //至此，右兄弟要么为空，要么太“瘦”

		// 情况3：左、右兄弟要么为空（但不可能同时），要么都太“瘦”――合并
	    if ( 0 < r ) { //与左兄弟合并
	        BTNodePosi<T> ls = p->child[r - 1]; //左兄弟必存在
	        ls->key.insert( ls->key.size(), p->key.remove( r - 1 ) ); p->child.remove( r );
	        // p的第r - 1个关键码转入ls，v不再是p的第r个孩子
	        ls->child.insert( ls->child.size(), v->child.remove( 0 ) );
	        if ( ls->child[ls->child.size() - 1] ) // v的最左侧孩子过继给ls做最右侧孩子
		        ls->child[ls->child.size() - 1]->parent = ls;
	        while ( !v->key.empty() ) { // v剩余的关键码和孩子，依次转入ls
	            ls->key.insert( ls->key.size(), v->key.remove( 0 ) );
	            ls->child.insert( ls->child.size(), v->child.remove( 0 ) );
	            if ( ls->child[ls->child.size() - 1] ) 
		            ls->child[ls->child.size() - 1]->parent = ls;
		    }
	        release ( v ); //释放v
	    } else { //与右兄弟合并
	        BTNodePosi<T> rs = p->child[r + 1]; //右兄弟必存在
	        rs->key.insert( 0, p->key.remove( r ) ); p->child.remove( r );
	        // p的第r个关键码转入rs，v不再是p的第r个孩子
	        rs->child.insert( 0, v->child.remove( v->child.size() - 1 ) );
	        if ( rs->child[0] ) rs->child[0]->parent = rs; // v的最右侧孩子过继给rs做最左侧孩子
	        while ( !v->key.empty() ) { // v剩余的关键码和孩子，依次转入rs
	            rs->key.insert( 0, v->key.remove( v->key.size() - 1 ) );
	            rs->child.insert( 0, v->child.remove( v->child.size() - 1 ) );
	            if ( rs->child[0] ) rs->child[0]->parent = rs;
	        }
		    release( v ); //释放v
	    }
	    v = p; //上升一层，如有必要则继续旋转或合并――至多O(logn)层
	} //while
}
```