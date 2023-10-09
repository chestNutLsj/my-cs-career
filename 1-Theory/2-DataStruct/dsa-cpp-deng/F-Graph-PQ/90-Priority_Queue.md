## 应用
应用举例
- 离散事件模拟
- 操作系统：任务调度/中断处理/MRU/...
- 输入法：词频调整

极值元素：须反复地、快速地定位
- 集合组成：可动态变化
- 元素优先级：可动态变化

查找极值元素，作为底层数据结构所支持的高效操作，是很多高效算法的基础
- 内部、外部、在线排序
- 贪心算法：Huffman编码、Kruskal
- 平面扫描算法中的事件队列

### ADT
```
template <typename T> struct PQ { //priority queue
	virtual void insert( T ) = 0;
	virtual T getMax() = 0;
	virtual T delMax() = 0;
}; //作为ADT的PQ有多种实现方式，各自的效率及适用场合也不尽相同

```
- Stack 和 Queue，都是 PQ 的特例——优先级完全取决于元素的插入次序
- Steap 和 Queap，也是 PQ 的特例——插入和删除的位置受限

## 实现
### Vector
![[90-Priority_Queue-vector-impl-pq.png]]

### List
![[90-Priority_Queue-List-impl-pq.png]]

### BBST
- AVL、Splay、Red-black：三个接口均只需 O(logn)时间，但是，BBST 的功能远远超出了 PQ 的需求... 
- PQ = 1 × insert () + 0.5 × search () + 0.5 × remove ()
- 若只需查找极值元，则不必维护所有元素之间的全序关系，偏序足矣，因此有理由相信，存在某种更为简单、维护成本更低的实现方式，使得各功能接口的时间复杂度依然为 O (logn)，而且实际效率更高。
- 当然，就最坏情况而言，这类实现方式已属最优——为什么？

### 完全二叉堆
![[90-Priority_Queue-complete-binary-heap.png]]
完全二叉堆的逻辑元素、物理节点依层次遍历次序彼此对应：
- 逻辑上等同于完全二叉树
- 物理上直接使用向量实现
- 内部节点的最大秩= $\lfloor \frac{n-2}{2}\rfloor$ = $\lceil \frac{n-3}{2}\rceil$ 
- 堆序性：只要 0<i，必满足 `H[i]<=H[Parent(i)]`，即 `H[0]` 已是全局最大者。

```
#define Parent(i) ( ((i) - 1) >> 1 )
#define LChild(i) ( 1 + ((i) << 1) )
#define RChild(i) ( (1 + (i)) << 1 )

template <typename T>
struct PQ_ComplHeap : public PQ<T>, public Vector<T> { //完全二叉堆
   PQ_ComplHeap() {} //默认构造
   PQ_ComplHeap( T* A, Rank n ) { copyFrom( A, 0, n ); heapify( _elem, n ); } //批量构造
   void insert( T ); //按照比较器确定的优先级次序，插入词条
   T getMax(); //读取优先级最高的词条
   T delMax(); //删除优先级最高的词条
}; // PQ_ComplHeap
template <typename T> void heapify( T* A, Rank n ); // Floyd建堆算法
template <typename T> Rank percolateDown( T* A, Rank n, Rank i ); //下滤
template <typename T> Rank percolateUp( T* A, Rank i ); //上滤

template <typename T> T PQ_ComplHeap<T>::getMax() { return _elem[0]; }

```

## 完全二叉堆的实现
### 插入
#### 逐层上滤
![[90-Priority_Queue-heap-insert.png]]

![[90-Priority_Queue-heap-insert-1.png]]
- 比较位置是： $i=\lfloor \frac{i-1}{2}\rfloor$;
- 父子的秩关系是：父 i，左子 2i+1，右子 2i+2（从 0 开始作秩）；

![[90-Priority_Queue-heap-insert-2.png]]

![[90-Priority_Queue-heap-insert-3.png]]

#### 代码
```
template <typename T> void PQ_ComplHeap<T>::insert( T e ) { //将词条插入完全二叉堆中
   Vector<T>::insert( e ); //将新词条接至向量末尾
   percolateUp( _elem, _size - 1 ); //再对该词条实施上滤调整
}

template <typename T> Rank percolateUp( T* A, Rank i ) { //对词条A[i]做上滤，0 <= i < _size
   while ( 0 < i ) { //在抵达堆顶之前，反复地
      Rank j = Parent( i ); //考查[i]之父亲[j]
      if ( lt( A[i], A[j] ) ) break; //一旦父子顺序，上滤旋即完成；否则
      swap( A[i], A[j] ); i = j; //父子换位，并继续考查上一层
   } //while
   return i; //返回上滤最终抵达的位置
}
```

#### 效率
- e 在上滤过程中，只可能与祖先们交换
- 完全树必平衡，e 的祖先不超过 O (logn)个
- 故知插入操作可在 O (logn)时间内完成
- 然而就数学期望而言，实际效率往往远远更高（因为不是总要上滤到根）

### 删除
#### 逐层下滤
![[90-Priority_Queue-heap-delete.png]]
- 取尾元素与被删除点——根作替换；
- 再逐层下滤尾元素直到不小于其后代；

![[90-Priority_Queue-heap-delete-1.png]]

![[90-Priority_Queue-heap-delete-2.png]]

![[90-Priority_Queue-heap-delete-3.png]]
- 每一次向后比较的秩有如下关系：比较 2i+1 处的元素

#### 代码
```
template <typename T> T PQ_ComplHeap<T>::delMax() { //取出最大词条
   swap( _elem[0], _elem[--_size] ); //堆顶、堆尾互换
   percolateDown( _elem, _size, 0 ); //新堆顶下滤
   return _elem[_size]; //返回原堆顶
}

//对向量前n个词条中的第i个实施下滤，i < n
template <typename T> Rank percolateDown( T* A, Rank n, Rank i ) {
   Rank j; // i及其（至多两个）孩子中，堪为父者
   while ( i != ( j = ProperParent( A, n, i ) ) ) //只要i非j，则
      swap( A[i], A[j] ), i = j; //二者换位，并继续考查下降后的i
   return i; //返回下滤抵达的位置（亦i亦j）
}
```

#### 效率
- e 在每一高度至多交换一次，累计交换不超过 O (logn)次
- 通过下滤，可在 O (logn)时间内，删除堆顶节点，并整体重新调整为堆
- 数学期望呢？O (logn)因为堆底的元素通常较小

### 建堆
#### 自上而下的上滤
```
template <typename T> void heapify( T* A, const Rank n ) { //蛮力建堆算法，O(nlogn)时间
   for ( Rank i = 1; i < n; i++ ) //自顶而下，依次
      percolateUp( A, i ); //经上滤插入各节点
}

```
![[90-Priority_Queue-heap-heapify-naive.png]]
- 从第一个节点开始，依次作为堆底，层层上滤；

**效率**：
- 最坏情况下，每个节点都需上滤至根（非递减序列），所需成本线性正比于其深度
- 即便只考虑底层，n/2 个叶节点，深度均为 O (logn) ，亦累计耗时 O (nlogn)
- 这样长的时间，本足以全排序！ 所以应该能够更快的

#### 自下而上的下滤
Floyd 建堆法：
1. 对任意给定的对 H0 和 H1，以及节点p
2. 为得到 $H0\cup \{p\}\cup H1$，只需将堆的根 r0 和 r1 分别作为 p 的左子或右子，再对 p 作下滤即可；
![[90-Priority_Queue-heap-heapify-floyd.png]]

```
template <typename T> void heapify( T* A, const Rank n ) { //Floyd建堆算法，O(n)时间
   for ( Rank i = n / 2 - 1; - 1 != i; i-- ) //自底而上，依次
      percolateDown( A, n, i ); //经下滤合并子堆
}//可理解为子堆的逐层合并，堆序性最终必然在全局恢复
```

![[90-Priority_Queue-heap-heapify-floyd-instance.png]]

**效率**：
- 每个内部节点所需的调整时间，正比于其高度而非深度
	- ![[90-Priority_Queue-heap-heapify-floyd-principle.png]]
- 不失一般性，考查满树：$n=2^{h+1}-1$ 
- 所有节点的高度总和 
$$
\begin{aligned}
S(n)&=\sum\limits_{k=1}^{h}k\cdot 2^{h-k}=\sum\limits_{k=1}^{h}\sum\limits_{i=1}^{k}2^{h-k}=\sum\limits_{i=1}^{h}\sum\limits_{k=i}^{h}2^{h-k}\\
&=\sum\limits_{i=1}^{h}\sum\limits_{k=0}^{h-i}2^{k}=\sum\limits_{i=1}^{h}(2^{h-i+1}-1)=\sum\limits_{i=1}^{h}2^{h-i+1}-h\\
&=\sum\limits_{i=1}^{h}2^{i}-h=2^{h+1}-2-h=O(n)
\end{aligned}
$$

>[! example] 对 n 个元素进行 Floyd 建堆，最多做多少次数据比较？（不使用 big-O-notation）
>思路：一个按层序遍历有序满二叉树能达到最多，在按照每层都要下坠底部为止。然后比较次数就是调整次数乘二。
> ![[90-Priority_Queue-floyd-heapify-quiz.png]]
>上面那个题我的理解是这样：最坏情况里，一个递增序列建成小顶堆，用下滤法从堆底一层层向上调整，可以预见堆顶只需要置换一次，第二层置换两次，依次取决于该层的节点数 2^(k-1)，每次调整都进行兄弟、父子两次比较（不是左右孩子），然后根据等比数列求和计算

### 思考
- Floyd 法在什么场合不适用？——在线建堆时
- 借助完全堆，可以在 O (nlogn)时间内构造 Huffman 树——小顶堆
- 大顶堆的 delMin 操作，能否在 O (logn)时间内完成？
	- 光凭 max-heap 是无法做到的，delMin 的时间复杂度是 O(n/2)+O (1)+O (logn)=O (n). 分析如下：
	- Essentially what you will need to do is search through all the leaf nodes of the implicit heap stored in the array. It will be a ==leaf node== of the heap because its parent must be larger than it (max heap property) and we know the leaves are stored from index n/2 and beyond (though this will not hurt our algorithmic complexity). So essentially what you should do is the following:
		- Search the array for the minimum element
		- Place the last-inserted heap element in the position of the minimum element (essentially this is the delete)
		- Upheap the replaced node to restore maximum heap property and correct storage of the heap in the array
	- This will take O (n) for the search of the minimum element, then O (1) for the switch, and finally O (log n) for the upheap. In total this is linear time, essentially the best you can do.
	- Remember to be careful with the the index operations, 2*i is the left child of node i and 2*i+1 is the right child of node i in an array based heap (assuming 0th element of the array is always empty and the root of the heap is at index 1)
	- 如果建立 [[92-Min-max-heap|min-max heap]]，则可以在 O (logn)时间内 delMin 和 delMax。


## 堆排序
### 思路
![[90-Priority_Queue-heapsort.png]]
- 在 selectionSort ()中 将 U 替换为H
- 初始化 ： heapify ()，O (n)
- 迭代 ： delMax ()，O (logn) 
- 不变性 ： H <= S
- 时间复杂度 O(n) + n x O(logn) = O(nlogn)

并且由于堆的结构，只需要 swap 就可以实现元素移动：
![[90-Priority_Queue-heapsort-swap.png]]
- 在物理上 完全二叉堆即是向量
- 既然此前有：`m = H[ 0 ]` , `x = H[ n − 1 ]` 
- 不妨随即就：swap ( m , x ) = H.insert ( x ) + S.insert ( m )

### 实现
```
template <typename T> void Vector<T>::heapSort( Rank lo, Rank hi ) { // 0 <= lo < hi <= size，就地堆排序
	T* A = _elem + lo; 
	Rank n = hi - lo; 
	heapify( A, n ); //将待排序区间建成一个完全二叉堆，O(n)
	
	while ( 0 < --n ) {
	//反复地摘除最大元并归入已排序的后缀，直至堆空
		swap( A[0], A[n] );
		percolateDown( A, n, 0 );
	} //堆顶与末元素对换，再下滤
}
```

![[90-Priority_Queue-heapsort-impl.png]]

### 实例
![[90-Priority_Queue-heapsort-instance.png]]
![[90-Priority_Queue-heapsort-instance-2.png]]
### 性能
| Algorithm | Average   | Best      | Worst     | Auxiliary Space | Stability  | Features         |
| --------- | --------- | --------- | --------- | --------- | ---------- | ---------------- |
| HeapSort  | O (nlogn) | O (nlogn) | O (nlogn) | O (1)     | not stable | 规模大时优势明显 |

- 使用堆排序，可以在不全排序的情况下找出前 k 个词条，即 O (klogn)的 selection 算法
- 权衡：就地是否值得——swap 操作涉及两个完整词条，操作的单位成本增加，且不能在线排序。

>[! note] Why heapsort unstable? Can it be improved?
>The heap sort algorithm is not a stable algorithm. This algorithm is not stable because the operations that are performed in a heap can change the relative ordering of the equivalent keys.
>More detailed: [Why Isn’t Heapsort Stable? | Baeldung on Computer Science](https://www.baeldung.com/cs/why-isnt-heapsort-stable)
>
>How to improve it?
>- 图的合成数解决 Prim 歧义的思想 [[82-Graph-Exercise#6-23 合成数法消除 Prim 和 Dijkstra 算法的歧义性|6-23 合成数法消除 Prim 和 Dijkstra 算法的歧义性]]。
>

## 锦标赛排序
### 胜者树
完全二叉树 - 叶节点： 参赛选手/团队 - 内部节点：孩子对决后之胜者 或有重复（连胜）  create() //O(n) remove() //O(logn) insert() //O(logn)  树根总是全局冠军：类似于堆顶
### 败者树

