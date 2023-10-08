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
