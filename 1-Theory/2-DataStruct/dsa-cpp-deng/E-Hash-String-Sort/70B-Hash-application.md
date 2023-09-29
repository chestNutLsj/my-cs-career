## 桶排序
### 使用情形
对\[0, m)区间内的 n 个互异整数借助散列表 H\[]作排序：
- 若 n<m ，即是表长>整数种类数，则所需空间 O (m)，所需时间 O (n)
	- ![[70-Hash-bucketsort-simple.png]]
- 若允许重复，m<<n，即是表长<<整数的数量（整数可以重复），使用独立链法，则可以得到空间复杂度 O (m+n)，时间复杂度 O (n)
	- ![[70-Hash-bucketsort-normal.png]]

### 最大缝隙问题
**描述**：任意 n 个互异点，将实轴分为 n-1 个有界区间，哪一段最长？
![[70-Hash-maxgap-problem.png]]

**暴力方法**：
- 对所有点排序； //Ω(nlogn)
- 依次计算相邻点对的间距，保留最大者； //Θ(n)

**桶策略**：
- 找到最左点和最右点； //O (n)
- 将有效范围均匀地划分为 n-1 个段（共 n 个桶）//O (n)
- 通过散列，将各点归入对应的桶 //O (n)
- 在各桶中，动态记录最左、最右点 //O (n)
- 算出相邻（非空）桶之间的距离 //O (n)
- 最大距离即是 MaxGap //O (n)

![[70-Hash-bucket-strategy-maxgap.png]]
- 图中红色区块表示区段内有点存在，亦即，在 MaxGap 问题中这个区间内只有最右者和最左者有价值，且该区段可以直接排除——不会是 MaxGap 的可选值；但反之，是 MinGapd 的可选值；


- 正确性：MaxGap 至少跨越两个桶——MaxGap 不能局限于某一个桶内；
- 对应的 MinGap 问题，如何确定 n-1 段有界区间中的最短区间：
	- 同样是桶策略，只需考查单个桶中最短的区间即可

## 基数排序
### 思路
**词典序**：关键码由多个域组成：$k_{d},k_{d-1},...,k_{1}$，将各域视作字母，则关键码即是单词——字典的排序方式。

由此导出基数排序的思想——自 $k_{1}$ 到 $k_{d}$，低位优先的顺序依次以各域为序做一趟桶排序：
![[70-Hash-radixSort.png]]
- 正确性：
	- 归纳假设：前 i 趟排序后，所有词条关于低 i 位有序
	- 假设前 i-1 趟均成立，则考查第 i 趟排序之后的情况，无非两种情况：
		- 凡第 i 位不同的词条，即便此前曾是逆序，现在必已转为有序；
		- 凡第 i 位相同的词条，**得益于桶排序的稳定性**，必保持原有次序（==如果底层排序算法不是稳定的，那么基数排序的结果不能保证准确==）；

- 时间成本=各趟桶排序的所需时间之和=
	- = $n+2m_1 + n+2m_2 + n+2m_3 + ... + n+2m_d$ //$m_k$是各域的取值范围
	- =O (d*(n+m)) //m=max{$m_1, m_2,..., m_d$}
	- 当 m=O (n)且 d 可视作常数时，可以达到 O (n)的效率！

### 实现
```
//以二进制无符号整数为例
using U = unsigned int; //约定：类型T或就是U；或可转换为U，并依此定序

template <typename T> //对列表中起始于位置p、宽度为n的区间做基数排序
void List<T>::radixSort(ListNodePosi<T> p, Rank n) { 
	// valid(p) && Rank(p) + n <= size

	ListNodePosi<T> head = p->pred; //待排序区间为(head, tail)
	ListNodePosi<T> tail = p; 
	for ( Rank i = 0; i < n; i++ ) tail = tail->succ;
	for ( U radixBit = 0x1; radixBit && (p = head); radixBit <<= 1 ) //以下反复地
	    for ( Rank i = 0; i < n; i++ ) //根据当前基数位，将所有节点
	        radixBit & U (p->succ->data) ? //分拣为后缀（1）与前缀（0）
	            insert( remove( p->succ ), tail ) : p = p->succ;
}//为避免remove、insert接口的低效率，可以拓展List::move(p,tail)接口，将节点p直接移至tail之前
```

![[70-Hash-radixSort-instance.png]]

### 对数密度的整数集排序
设 d>1 为常数，考查取自\[0, n^d)范围内的 n 个整数：
- 常规密度= $\frac{n}{n^{d}}=\frac{1}{n^{(d-1)}}\sim 0$ ;
- 对数密度= $\frac{\ln n}{\ln n^{d}}=\frac{1}{d}=O(1)$
- 即这类整数集的对数密度不超过常数；
- 若取 d=4，则即便是 64 位的 long 型整数，也只需 $n>(2^{64})^{\frac{1}{4}}=2^{16}=65536$ 
- 对于这类整数集，设计效率 o (nlogn)的排序算法。

利用基数排序设计线性排序算法：
- 预处理：将所有元素转换为 n 进制形式 $x=(x_d,..., x_2, x_1)$ 
- 于是，每个元素均转化为 d 个域，故可直接套用 Radixsort 算法
- 排序时间=d (n+n)=O (n)
- 突破此前确定的Ω(nlogn)下界的原因：整数取值范围有限制，且不再是基于比较的计算模式；
- 并且进制转换需要 O (n * d)时间

## 计数排序
### 思路
如何优化“小集合+大数据”的对数密度的数据集的排序？

![[70B-Hash-application-porkr.png]]
以纸牌排序为例 (n>>m=4)，假设已按点数排序，接下来按花色排序：
![[70B-Hash-application-countingSort.png]]
1. 经过分桶，统计出各种花色的数量; //O (n)
2. 自前向后扫描个桶，依次累加，即可确定各套花色所处的秩的区间：$[0,3)+[3,5)+[5,9)+[9,11)$ //O (m)
	- ![[70B-Hash-application-countingSort2.png]]
3. 自后向前扫描每一张牌，对应桶的计数减一，即是其所在最终有序序列中对应的秩; //O (n)

### 举例
![[70B-Hash-application-countsort-instance-1.png]]

![[70B-Hash-application-countsort-instance-2.png]]

![[70B-Hash-application-countsort-instance-3.png]]

![[70B-Hash-application-countsort-instance-4.png]]

![[70B-Hash-application-countsort-instance-5.png]]

![[70B-Hash-application-countsort-instance-6.png]]

![[70B-Hash-application-countsort-instance-7.png]]

![[70B-Hash-application-countsort-instance-8.png]]

![[70B-Hash-application-countsort-instance-9.png]]

![[70B-Hash-application-countsort-instance-10.png]]

![[70B-Hash-application-countsort-instance-11.png]]

### 分析
- 时间复杂度=O (n+m+n) // 高效处理大规模数据
- 空间复杂度=O (n)
- 最后一步的扫描次序，可否改为自前向后？
	- 可以
	- 答案来自《算法导论》的习题 8-2-3：The algorithm still works correctly. The order that elements are taken out of C and put into B doesn’t affect the placement of elements with the same key. It will still fill the interval (C\[k − 1], C\[k]] with elements of key k. 
	- The question of whether it is stable or not is not well phrased. In order for stability to make sense, we would need to be sorting items which have information other than their key, and the sort as written is just for integers, which don’t. We could think of extending this algorithm by placing the elements of A into a collection of elements for each cell in array C. Then, if we use a FIFO collection, the modification of line 10 will make it stable, if we use LILO, it will be anti-stable.


## 跳转表
Skip lists are data structures that use probabilistic balancing rather than strictly enforced balancing.

Algorithms for insertion and deletion in skip lists are much simpler and significantly faster than equivalent algorithms for balanced trees.

### 结构
![[70B-Hash-application-skiplist.png]]
- 分层耦合的多个列表：$S_0, S_1, ..., S_h$ ，$S_0$ 是底层，$S_h$ 是顶层
- 横向为层 level: prev () , next () ，设有头尾哨兵
- 纵向成塔 tower: above (), below ()

### 实现
#### 四联表
![[70B-Hash-application-quadlist.png]]
- 跳转表本身是四联表，由四联节点组成，四联节点指其左、右、上、下都有指针，分别为 x 的前驱、后继、上邻、下邻；
```
/***************************
 * QuadList interface
****************************/
template <typename T> struct Quadlist { //四联列表
	Rank _size; //规模
	QNodePosi<T> header, trailer; //头哨兵、尾哨兵
	void init(); //初始化
	int clear(); //清除所有节点
	Quadlist() { init(); } //构造
	~Quadlist() { clear(); delete header; delete trailer; } //析构
	T remove( QNodePosi<T> p ); //删除（合法）位置p处的节点，返回被删除节点的数值
	QNodePosi<T> insert( T const& e, QNodePosi<T> p, QNodePosi<T> b = NULL ); //将e作为p的后继、b的上邻插入
	void traverse( void (* ) ( T& ) ); //遍历各节点，依次实施指定操作（函数指针，只读或局部修改）
	template <typename VST> void traverse ( VST& ); //遍历
}; //Quadlist

/***************************
 * QuadNode implementation
****************************/
template <typename T> struct QNode;
template <typename T> using QNodePosi = QNode<T>*; //跳转表节点位置
template <typename T> struct QNode { //四联节点
	T entry; //所存词条
	QNodePosi<T> pred, succ, above, below; //前驱、后继、上邻、下邻
	QNode( T e = T(), QNodePosi<T> p = NULL, QNodePosi<T> s = NULL,QNodePosi<T> a = NULL, QNodePosi<T> b = NULL ) //构造器
      : entry( e ), pred( p ), succ( s ), above( a ), below( b ) {}
	QNodePosi<T> insert( T const& e, QNodePosi<T> b = NULL ); //将e作为当前节点的后继、b的上邻插入
};
template <typename T> QNodePosi<T> //将e作为当前节点的后继、b的上邻插入Quadlist
QNode<T>::insert( T const& e, QNodePosi<T> b ) {
	QNodePosi<T> x = new QNode<T>( e, this, succ, NULL, b ); //创建新节点
	succ->pred = x; succ = x; //设置水平逆向链接
	if ( b ) b->above = x; //设置垂直逆向链接
	return x; //返回新节点的位置
}

/***************************
 * QuadList::init()
****************************/
template <typename T> void Quadlist<T>::init() { //Quadlist初始化，创建Quadlist对象时统一调用
   header = new QNode<T>; //创建头哨兵节点
   trailer = new QNode<T>; //创建尾哨兵节点
   header->succ = trailer; header->pred = NULL; //沿横向联接哨兵
   trailer->pred = header; trailer->succ = NULL; //沿横向联接哨兵
   header->above = trailer->above = NULL; //纵向的后继置空
   header->below = trailer->below = NULL; //纵向的前驱置空
   _size = 0; //记录规模
} //如此构造的四联表，不含任何实质的节点，且暂时与其它四联表相互独立

/***************************
 * QuadList::insert()
****************************/
template <typename T> QNodePosi<T> //将e作为p的后继、b的上邻插入Quadlist
Quadlist<T>::insert( T const& e, QNodePosi<T> p, QNodePosi<T> b )
{ _size++; return p->insert( e, b ); } //返回新节点位置（below = NULL）

/***************************
 * QuadList::traverse()
****************************/
template <typename T> //遍历Quadlist，对各节点依次实施visit操作
void Quadlist<T>::traverse( void ( *visit )( T& ) ) { //利用函数指针机制，只读或局部性修改
	QNodePosi<T> p = header;
	while ( ( p = p->succ ) != trailer ) visit( p->data );
}

template <typename T> template <typename VST> //遍历Quadlist，对各节点依次实施visit操作
void Quadlist<T>::traverse( VST& visit ) { //利用函数对象机制，可全局性修改
	QNodePosi<T> p = header;
	while ( ( p = p->succ ) != trailer ) visit( p->data );
}

/***************************
 * QuadList::remove()
****************************/
template <typename T> //删除Quadlist内位置p处的节点，返回其中存放的词条
T Quadlist<T>::remove( QNodePosi<T> p ) { // assert: p为Quadlist中的合法位置
   p->pred->succ = p->succ; p->succ->pred = p->pred; _size--;//摘除节点
   T e = p->entry; delete p; //备份词条，释放节点
   return e; //返回词条
}

/***************************
 * QuadList::clear()
****************************/
template <typename T>
int Quadlist<T>::clear() { //清空Quadlist
	int oldSize = _size;
	while ( 0 < _size )
		remove( header->succ ); //逐个删除所有节点
	return oldSize;
}

```

#### 跳转表
由此，构建跳转表：
```
/***************************
 * SkipList interface
****************************/
#include "List/List.h" //引入列表
#include "Entry/Entry.h" //引入词条
#include "Quadlist.h" //引入Quadlist
#include "Dictionary/Dictionary.h" //引入词典

template <typename K, typename V> //key、value
//符合Dictionary接口的Skiplist模板类（隐含假设元素之间可比较大小）
struct Skiplist : public Dictionary<K, V>, public List< Quadlist< Entry<K, V> >* > {
   Skiplist() { insertAsFirst( new Quadlist< Entry<K, V> > ); }; //即便为空，也有一层空列表
   QNodePosi< Entry<K, V> > search( K ); //由关键码查询词条
   Rank size() const { return empty() ? 0 : last()->data->_size; } //词条总数
   Rank height() { return List::size(); } //层高 == #Quadlist
   V* get( K ); //读取
   bool put(K, V); //插入（Skiplist允许词条重复，故必然成功）
   bool remove ( K ); //删除
};

/***************************
 * SkipList::get()
 * 读取词条
****************************/
template <typename K, typename V>
V* Skiplist<K, V>::get( K k ) {
	QNode< Entry<K, V> >* p = search(k); //无论是否命中，search()都不会返回NULL
	return (p->pred && p->entry.key == k) ? &(p->entry.value) : NULL; //故须再做判断
} //有多个命中时靠后者优先
```

### 空间性能
- 逐层随机减半：$S_k$ 中每个关键码，在 $S_{k+1}$ 中出现的概率均为 1/2
- 因此各塔的高度符合几何分布：$Pr(h=k)=p^{k-1}\cdot(1-p)$ 
- 于是塔高的期望：$E(h)=\frac{1}{1-p}=2$
- 更直观的解释：
	- 既然逐层随机减半，故 $S_0$ 中任意关键码在 $S_k$ 中仍然出现的概率为 $2^{-k}$ ，则第 k 层节点数的期望值 $E(|S_{k}|)=n\cdot 2^{-k}=\frac{n}{2^{k}}$ 
	- 于是所有节点期望的总数——各层列表所需空间的总和为 $E(\sum\limits_{k}|S_{k}|)=\sum\limits_{k}E(|S_{k}|)=n\times\sum\limits_{k}2^{-k}<2n=O(n)$ 
	- 于是，跳转表的所需空间为 expected-O (n)，**不会因为塔高而显著影响空间复杂度**
	- 类比，半衰期为 1 年的放射性物质中，各粒子的平均寿命不过 2年

### 查找
![[70B-Hash-application-skiplist-search.png]]
- 减治思路——由高到低，类似二分查找

![[70B-Hash-application-skiplist-search2.png]]
- 查找时间取决于横向、纵向累积的跳转次数，因此考察跳转次数：
- 纵向跳转次数（塔高，最坏情况）：
	- 观察得知，一座塔的高度不低于 k (层数) 的概率为 p^k，因此随着 k 的增加，$S_k$ 为空的概率急剧上升：$Pr(|S_{k}=0|)=(1-p^{k})^{n}\ge 1-n\cdot p^{k}$ 
	- 由此可知，跳转表高度 h=O (logn)的概率极大
	- 举例，若 p=1/2，则 $k=3\cdot\log_\frac{1}{p}n$ 层非空的概率为 $Pr(|S_{k}|>0)\le n\cdot p^{k}=n\cdot n^{-3}=\frac{1}{n^{2}}\sim 0$ 
	- 因此，查找过程中，**纵向跳转的次数累计不超过 expected-O (logn)**
- 横向跳转次数（）：
	- 观察得知：横向跳转所经的节点必然依次紧邻，而且每次抵达都是塔顶
	- 于是将沿同一层列表跳转的次数记作 Y，则符合几何分布 $Pr(Y=k)=(1-p)^{k}$ 
	- 几何分布的期望为 $E(Y)=\frac{1-p}{p}=\frac{1-0.5}{0.5}=1$ 次, 即同一层列表中连续跳转的期望时间成本 = 1次跳转 + 1次驻足 = 2
- 故跳转表的每次查找，都可在 expected-(2h)=expected-O (logn)时间内完成

```
/***************************
 * SkipList::search()
 * 返回关键码不大于k的最后一个词条（所对应塔的基座）
****************************/
template <typename K, typename V>
QNodePosi<Entry<K, V>> Skiplist<K, V>::search(K k ) {
	for ( QNodePosi<Entry<K, V>> p = first()->data->header; ; ) //从顶层Quadlist的首节点p出发
	    if ( ( p->succ->succ ) && ( p->succ->entry.key <= k ) ) p = p->succ; //尽可能右移
	    else if ( p->below ) p = p->below; //水平越界时，下移
	    else return p; //验证：此时的p符合输出约定（可能是最底层列表的header）
}
```

### 插入与删除
![[70B-Hash-application-skiplist-insert-remove.png]]

```
/***************************
 * SkipList::put()
 * 插入词条(SkipList允许词条重复，故必然成功)
****************************/
template <typename K, typename V>
bool Skiplist<K, V>::put( K k, V v ) { 
	Entry<K, V> e = Entry<K, V>( k, v ); //待插入的词条（将被同一塔中所有节点共用）
	QNodePosi< Entry<K, V> > p = search( k ); //查找插入位置：新塔将紧邻其右，逐层生长
	ListNodePosi< Quadlist< Entry<K, V> >* > qlist = last(); //首先在最底层
	QNodePosi< Entry<K, V> > b = qlist->data->insert( e, p ); //创建新塔的基座
	while ( rand() & 1 ) { //经投掷硬币，若确定新塔需要再长高，则
	    while ( p->pred && !p->above ) p = p->pred; //找出不低于此高度的最近前驱
		if ( !p->pred && !p->above ) { //若该前驱是header，且已是最顶层，则
			insertAsFirst( new Quadlist< Entry<K, V> > ); //需要创建新的一层
			first()->data->header->below = qlist->data->header;
			qlist->data->header->above = first()->data->header;
		}
	    p = p->above; qlist = qlist->pred; //上升一层，并在该层
	    b = qlist->data->insert( e, p, b ); //将新节点插入p之后、b之上
	} //课后：调整随机参数，观察总体层高的相应变化
	return true; //Dictionary允许重复元素，插入必成功
}

/***************************
 * SkipList::remove()
 * 删除词条
****************************/
template <typename K, typename V>
bool Skiplist<K, V>::remove( K k ) { 
	QNodePosi< Entry<K, V> > p = search ( k ); //查找目标词条
	if ( !p->pred || (k != p->entry.key) )  return false; //若不存在，直接返回
	ListNodePosi< Quadlist< Entry<K, V> >* > qlist = last(); //从底层Quadlist开始
	while ( p->above ) { qlist = qlist->pred; p = p->above; } //升至塔顶
	do { //逐层拆塔
	    QNodePosi< Entry<K, V> > lower = p->below; //记住下一层节点，并
	    qlist->data->remove( p ); //删除当前层节点，再
	    p = lower; qlist = qlist->succ; //转入下一层
	} while ( qlist->succ ); //直到塔基
	while ( (1 < height()) && (first()->data->_size < 1) ) { //逐层清除
	    List::remove( first() ); 
	    first()->data->header->above = NULL;
	} //已不含词条的Quadlist（至少保留最底层空表）
	return true; //删除成功
}
```