## 快速排序
### 思路
![[B0-Sort-quicksort-idea.png]]
- 前缀、后缀各自（递归）排序之后，原序列便自然有序 
- sorted (S) = sorted (SL) + pivot + sorted (SR)
- mergesort 难点在于合，而 quicksort 在于分
### 递归实现
```
template <typename T> //向量快速排序
void Vector<T>::quickSort( Rank lo, Rank hi ) { // 0 <= lo < hi <= size
   if ( hi - lo < 2 ) return; //单元素区间自然有序，否则...
   Rank mi = partition( lo, hi ); //在[lo, hi)内构造轴点
   quickSort( lo, mi ); quickSort( mi + 1, hi ); //前缀、后缀各自递归排序
}
```

### 轴点选取

快速排序与轴点选取：
- 序列有序的必要条件： 轴点必定已然就位
- 特别地：在有序序列中，所有元素皆为轴点，反之亦然
- 快速排序：就是将所有元素逐个转换为轴点的过程
- 坏消息：在原始序列中，轴点未必存在
	- derangement： 任何元素都不在原位 
		- 比如，顺序序列循环移位
		- ![[B0-Sort-quicksort-circular-shift.png]]
- 好消息：不需很多交换，即可使任一元素转为轴点
- 问题： 如何交换？成本多高？

#### 轴点划分：LUG 版
思路：减治
- ![[B0-Sort-quicksort-LUG.png]]
- 任取一个候选者（如 `[0]`）
- 将序列划分为三段：L + U + G，分别是小于、为扫描到的、大于部分 
- **交替地**向内移动 lo 和 hi
- 逐个检查当前元素：
	- 若更小/大，则转移归入 L/G 
	- 当 lo = hi 时，只需将候选者嵌入于 L、G 之间，即成轴点！
- 各元素最多移动一次（候选者两次） ——累计 O(n)时间、O(1)辅助空间

```
template <typename T> //通过调整元素位置，构造出区间[lo, hi)内的一个轴点
Rank Vector<T>::partition( Rank lo, Rank hi ) { // LUG版：基本形式
   swap( _elem[lo], _elem[lo + rand() % ( hi - lo )] ); //任选一个元素与首元素交换
   T pivot = _elem[lo]; //经以上交换，等效于随机选取候选轴点
   while ( lo < hi ) { //从两端交替扫描，直至相遇
      do hi--; while ( ( lo < hi ) && ( pivot <= _elem[hi] ) ); //向左拓展后缀G
      if ( lo < hi ) _elem[lo] = _elem[hi]; //阻挡者归入前缀L，即凡小于轴点者，归入L部分
      do lo++; while ( ( lo < hi ) && ( _elem[lo] <= pivot ) ); //向右拓展前缀L
      if ( lo < hi ) _elem[hi] = _elem[lo]; //阻挡者归入后缀G，即凡大于轴点者，归入G
   } // assert: quit with lo == hi or hi + 1
   _elem[hi] = pivot; //候选轴点置于前缀、后缀之间，它便名副其实
   return hi; //返回轴点的秩
}
```

![[B0-Sort-quicksort-LUG-2.png]]
- `L <= pivot <= G` 且 `U=[lo,hi)` 中，`[lo]` 和 `[hi]` 交替空闲
- 故序列中 U 部分单调减少，L 和 G 部分单调增加，这趟扫描一定能结束并正确归位 pivot

![[B0-Sort-quicksort-LUG-instance.png]]
分析：
- 线性时间 
	- 尽管 lo 、hi 交替移动，但累计移动距离不过 O (n) 
- in -place
	- 只需 O (1)附加空间
- unstable
	- lo/hi 的移动方向相反，左/右侧的大/小重复元素 可能前/后颠倒

#### 轴点划分：DUP 版
思考多个雷同元素时，仍使用 LUG 策略的快速排序的情况：
![[B0-Sort-quicksort-worst.png]]
- 有大量元素与轴点雷同时，切分点将接近于 lo，划分极度失衡
- 更糟糕的是，这一最坏情况可能会持续发生，整个算法过程**等效地退化为线性递归**，
- 递归深度接近于 O (n) ，运行时间接近于 O (n^2) 
- 直觉地改进，若移动 lo 和 hi 的过程中，同时比较相邻元素，若属于相邻的重复元素，则不再深入递归，但一般情况下，如此计算量反而增加，得不偿失

实际上，LUG 版略做调整，即可解决问题：
```
template <typename T> //通过调整元素位置，构造出区间[lo, hi)内的一个轴点
Rank Vector<T>::partition( Rank lo, Rank hi ) { // DUP版：可优化处理多个关键码雷同的退化情况
   swap( _elem[lo], _elem[lo + rand() % ( hi - lo )] ); //任选一个元素与首元素交换
   T pivot = _elem[lo]; //经以上交换，等效于随机选取候选轴点
   while ( lo < hi ) { //从两端交替扫描，直至相遇
      do hi--; while ( ( lo < hi ) && ( pivot < _elem[hi] ) ); //向左拓展后缀G，与LUG版相比，pivot的判断条件从<=变为<
      if ( lo < hi ) _elem[lo] = _elem[hi]; //阻挡者归入前缀L，即凡不大于轴点者，归入L部分
      do lo++; while ( ( lo < hi ) && ( _elem[lo] < pivot ) ); //向右拓展前缀L，同样这里由LUG版的<=变为<
      if ( lo < hi ) _elem[hi] = _elem[lo]; //阻挡者归入后缀G，即凡不小于轴点者，归入G部分
   } // assert: quit with lo == hi or hi + 1
   _elem[hi] = pivot; //候选轴点置于前缀、后缀之间，它便名副其实
   return hi; //返回轴点的秩
}
```

![[B0-Sort-quicksort-DUP.png]]
- 优点：
	- 可以正确地处理一般情况和重复元素较多时情况，同时复杂度并未实质增高
	- 遇到连续的重复元素时， lo 和 hi 会交替移动， 切分点接近于 (lo+hi)/2 
	- 由 LUG 版的勤于拓展、懒于交换，转为懒于拓展、勤于交换 
- 缺点：
	- 交换操作有所增加， “更”不稳定

#### 轴点划分：LGU 版
![[B0-Sort-quicksort-LGU.png]]
```
template <typename T> //轴点构造算法：通过调整元素位置构造区间[lo, hi)的轴点，并返回其秩
Rank Vector<T>::partition( Rank lo, Rank hi ) { // LGU版
   swap( _elem[lo], _elem[lo + rand() % ( hi - lo )] ); //任选一个元素与首元素交换
   T pivot = _elem[lo]; //以首元素为候选轴点――经以上交换，等效于随机选取
   Rank mi = lo;
   //++++++++++++++++++++++++++++++++++++++++++++++++++
   //--- L<[lo] --- ] --- [lo]<=G --- ][ --- Unknown ---
   //X x . . . .  . x . . . . .. . . . x . . .. . . . x X
   //|              |                  |                |
   //lo             mi          k                hi
   //(lo is pivot candidate)+++++++++++++++++++++++++++
   
   for ( Rank k = lo + 1; k < hi; k++ ) //自左向右扫描
      if ( _elem[k] < pivot ) //若当前元素_elem[k]小于pivot，则
         swap( _elem[++mi], _elem[k] ); //将_elem[k]交换至原mi之后，使L子序列向右扩展
   //++++++++++++++++++++++++++++++++++++++++++++++++++++
   //--- L<[lo] ---- ] ------------- [lo] <= G -----]
   //X x . . . . . . x . . . . . . . . . . . . .. . x X
   // |              |                                |
   // lo            mi                                hi
   // (lo is pivot candidate) +++++++++++++++++++++++++
   
   swap( _elem[lo], _elem[mi] ); //候选轴点归位
   return mi; //返回轴点的秩
}
```

![[B0-Sort-quicksort-LGU-instance.png]]
### 迭代实现

#### 思路
递归版快速排序的空间复杂度取决于递归深度，
- 最好情况：划分总是均衡的     // `O(logn)`
	- ![[B0-Sort-quicksort-space-1.png]]
- 最坏情况：划分总是偏侧于一端 // `O(n)`
	- ![[B0-Sort-quicksort-space-2.png]]
- 平均情况：均衡不致太少      // `O(logn)`
	- ![[B0-Sort-quicksort-space-3.png]]

如何避免最坏情况呢？

#### 迭代版快速排序代码：
```
#define Put( K, s, t ) { if ( 1 < (t) - (s) ) { K.push(s); K.push(t); } }
#define Get( K, s, t ) { t = K.pop(); s = K.pop(); }

template <typename T> //向量快速排序
void Vector<T>::quickSort( Rank lo, Rank hi ) { //0 <= lo < hi <= size
   Stack<Rank> Task; Put( Task, lo, hi );// 等效于对递归树的先序遍历
   while ( !Task.empty() ) {
      Get( Task, lo, hi );
      Rank mi = partition( lo, hi ); //在[lo, hi)内构造轴点
      if ( mi - lo < hi - mi ) { Put( Task, mi+1, hi ); Put( Task, lo, mi ); }
      else                     { Put( Task, lo, mi ); Put( Task, mi+1, hi ); }
   } //大任务优先入栈（小任务优先出栈执行），可保证递归深度（空间成本）不过O(logn)
}

```

#### 性能分析
- 最好情况：每次划分都（接近）平均，轴点总是（接近）中央 —— 到达下界！
	- $T(n)=2\cdot T(\frac{n-1}{2})+O(n)=O(n\log n)$ 
- 最坏情况：每次划分都极不均衡（比如，轴点总是最小/大元素） —— 与起泡排序相当！
	- $T(n)=T(n-1)+T(0)+O(n)=O(n^{2})$ 
- 采用随机选取（Randomization）、三者取中（Sampling）之类的策略 只能降低最坏情况的概率，而无法杜绝 

既如此，为何还称作快速排序？

### 分析递归深度
![[B0-Sort-quicksort-recurse-depth.png]]
- 最坏情况（Ω(n) 递归深度），概率极低
- 平均情况（O (logn) 递归深度），概率极高
- 实际上：只要 pivot 并非过于侧偏，都会有效地缩短递归深度，因此不妨定义：
- **准居中**：pivot 落在宽度为 $λ\cdot n$ 的居中区间（ λ 也是这种情况出现的概率）
	- 则，每一递归路径上，至多出现 $\log_{\frac{2}{1+\lambda}}n$ 个准居中的 pivots
	- 因此，每递归一层，都有 λ（ 1-λ ）的概率准居中（准偏侧）
	- 深入 $\frac{1}{\lambda}\cdot\log_{\frac{2}{1+\lambda}}n$ 层后，可期望出现 $\log_{\frac{2}{1+\lambda}}n$ 次准居中，且有极高概率出现；
	- 相反情况的概率则 < $(1-\lambda)^{(\frac{1}{\lambda}-1)\cdot\log_{\frac{2}{1+\lambda}}n}=n^{(\frac{1}{\lambda}-1)\cdot\log_{\frac{2}{1+\lambda}}n}$ ，其随着 λ 的增加而下降，
	- 比如 λ>1/3 后，即至少有 $1-n^{2\cdot\log_{\frac{3}{2}} \frac{2}{3}}=1-n^{-2}$ 的概率，使得递归深度不超过 $\frac{1}{\lambda}\cdot\log_{\frac{2}{1+\lambda}}n= 3\cdot\log_\frac{3}{2}n\approx 5.129\log n$ 

### 分析比较次数
#### 递推分析
![[B0-Sort-quicksort-compare-times-1.png]]

- $T (n)\approx 1.386\cdot n\log n$ 

#### 后向分析
![[B0-Sort-quicksort-compare-times-2.png]]

- $T(n)\le 2\cdot n\ln n$ 


## k 选取
### 问题描述
![[B0-Sort-k-selection.png]]
- **k 选取问题**：在任意一组可比较大小的元素中，如何由小到大，找到次序为 k 者？ 亦即，在这组元素的非降排序序列 S 中，找出 `S[k]`。
- **中位数选取**：长度为 n 的有序序列 S 中，元素 $S[\lfloor \frac{n}{2}\rfloor]$ 称作中位数，在任意一组可比较大小的元素中，如何找到中位数？
	- 中位数选取是一个 k 选取的特例，也是难度最大者

### 众数与中位数
无序向量中，若有**一半以上**元素同为 m，则称之为众数
- 在 {3,5,2,3,3} 中，众数为3；
- 在 {3,5,2,3,3,0} 中，却无众数

如何找到众数？
- 平凡算法：排序 + 扫描 // `O(nlogn+n)` 
- 但进一步地，若限制时间不超过 O (n)，附加空间不超过 O (1)呢？ 
	- 必要性：众数若存在，则亦为必中位数
	- 事实上：只要能够找出中位数，即不难验证它是否众数：
```
template <typename T>
bool majority( Vector<T> A, T & maj ){
	return majEleCheck( A, maj = median( A ) ); // 验证是否为中位数
}
```

然而，在高效的中位数算法未知之前，如何确定众数的候选呢？——减治
![[B0-Sort-k-selection-major.png]]
- 若在向量 A 的前缀 P（|P|为偶数）中，元素 x 出现的次数恰占半数，则 A 有众数，仅当对应的后缀 A − P 有众数 m，且 m 就是 A 的众数 
- 既然最终总要花费 O(n)时间做验证，故而只需考虑 A 的确含有众数的两种情况：
	1. 若 x = m，则在排除前缀 P 之后，m 与其它元素在数量上的差距保持不变 
	2. 若 x != m，则在排除前缀 P 之后，m 与其它元素在数量上的差距不致缩小 
- 若将众数的标准从“一半以上”改作“至少一半”，算法需做什么调整？——调整等号，在 `majEleCheck` 中的判断之处

众数查找算法的实现：
```
template <typename T>
bool majority( Vector<T> A, T& maj ) { //众数查找算法：T可比较可判等
   maj = majEleCandidate( A ); //必要性：选出候选者maj
   return majEleCheck( A, maj ); //充分性：验证maj是否的确当选
}

template <typename T> T majEleCandidate( Vector<T> A ) { //选出具备必要条件的众数候选者
   T maj; //众数候选者
// 线性扫描：借助计数器c，记录maj与其它元素的数量差额
   for ( Rank c = 0, i = 0; i < A.size(); i++ )
      if ( 0 == c ) { //每当c归零，都意味着此时的前缀P可以剪除
         maj = A[i]; c = 1; //众数候选者改为新的当前元素
      } else //否则
         maj == A[i] ? c++ : c--; //相应地更新差额计数器
   return maj; //至此，原向量的众数若存在，则只能是maj ―― 尽管反之不然
}

template <typename T> bool majEleCheck ( Vector<T> A, T maj ) { //验证候选者是否确为众数
   Rank occurrence = 0; //maj在A[]中出现的次数
   for ( Rank i = 0; i < A.size(); i++ ) //逐一遍历A[]的各个元素
      if ( A[i] == maj ) occurrence++; //每遇到一次maj，均更新计数器
   return 2 * occurrence > A.size(); //根据最终的计数值，即可判断是否的确当选
}
```

### 中位数选取
归并向量的中位数如何找到？任给有序向量 $S_1$ 和 $S_2$，长度为 $n_1$ 和 $n_2$，如何快速找出 $S=S_{1}\cup S_{2}$ 的中位数：
- ![[B0-Sort-k-selection-merge-median.png]]
- 蛮力：经归并得到有序向量 S，取 $S[\frac{n_{1}+n_{2}}{2}]$ 即是；
	- 如此共需 $O(n_1+n_2)$ 时间，但未能利用两个向量的有序性；
- 减治思路：
	- 考查 $m_{1}=S_{1}[\lfloor \frac{n}{2}\rfloor]$ 和 $m_{2}=S_{2}[\lceil \frac{n}{2}\rceil-1]=S_{2}[\lfloor \frac{n-1}{2}\rfloor]$ 
	- ![[B0-Sort-k-selection-median-idea.png]]
	- 若 $m_1=m_2$，则它们同为 $S_1,S_2,S$ 的中位数
	- 若 $m_1<m_2$，则无论 n 为奇偶，必有 $median(S_{1}\cup S_{2})=median(S_{1}.suffix(\lceil \frac{n}{2}\rceil)\cup S_{2}.prefix(\lceil \frac{n}{2}\rceil))$ ，这意味着，如此减除（一半规模）之后，中位数不变
	- 若 $m_1>m_2$ 同理；
	- 故经上思路不断剪枝，可以得到 `O(log(min(n1,n2)))` 时间内找到中位数的算法：
```
//等长子向量
template <typename T> // S1[lo1, lo1 + n)和S2[lo2, lo2 + n)分别有序，n > 0，数据项可能重复
T median( Vector<T>& S1, Rank lo1, Vector<T>& S2, Rank lo2, Rank n ) { //中位数算法（高效版）
   if ( n < 3 ) return trivialMedian( S1, lo1, n, S2, lo2, n ); //递归基
   Rank mi1 = lo1 + n / 2, mi2 = lo2 + ( n - 1 ) / 2; //长度（接近）减半
   if ( S1[mi1] < S2[mi2] )
      return median ( S1, mi1, S2, lo2, n + lo1 - mi1 ); //取S1右半、S2左半
   else if ( S1[mi1] > S2[mi2] )
      return median ( S1, lo1, S2, mi2, n + lo2 - mi2 ); //取S1左半、S2右半
   else
      return S1[mi1];
}

// 中位数算法蛮力版：效率低，仅适用于max(n1, n2)较小的情况
template <typename T> //子向量S1[lo1, lo1 + n1)和S2[lo2, lo2 + n2)分别有序，数据项可能重复
T trivialMedian( Vector<T>& S1, Rank lo1, Rank n1, Vector<T>& S2, Rank lo2, Rank n2 ) {
   Rank hi1 = lo1 + n1, hi2 = lo2 + n2;
   Vector<T> S; //将两个有序子向量归并为一个有序向量
   while ( ( lo1 < hi1 ) && ( lo2 < hi2 ) )
      S.insert( S1[lo1] <= S2[lo2] ? S1[lo1++] : S2[lo2++] );
   while ( lo1 < hi1 ) S.insert( S1[lo1++] );
   while ( lo2 < hi2 ) S.insert( S2[lo2++] ); 
   return S[( n1 + n2 ) / 2]; //直接返回归并向量的中位数
}

//任意长度的子向量
template <typename T> //向量S1[lo1, lo1 + n1)和S2[lo2, lo2 + n2)分别有序，数据项可能重复
T median ( Vector<T>& S1, Rank lo1, Rank n1, Vector<T>& S2, Rank lo2, Rank n2 ) { //中位数算法
   if ( n1 > n2 ) return median( S2, lo2, n2, S1, lo1, n1 ); //确保n1 <= n2
   if ( n2 < 6 ) //递归基：1 <= n1 <= n2 <= 5
      return trivialMedian( S1, lo1, n1, S2, lo2, n2 );
   //////////////////////////////////////////////////
   //                 lo1     lo1 + n1/2  lo1 + n1 - 1
   //                 |       |           |
   //                 X >>>>> X >>>>>>>>> X
   // Y .. trimmed .. Y >>>>> Y >>>>>>>>> Y .. trimmed .. Y
   // |               |       |           |               |
   // lo2   lo2+(n2-n1)/2  lo2+n2/2 lo2+(n2+n1)/2  lo2+n2-1
   //////////////////////////////////////////////////
   
   if ( 2 * n1 < n2 ) //若两个向量的长度相差悬殊，则长者（S2）的两翼可直接截除
      return median( S1, lo1, n1, S2, lo2 + ( n2 - n1 - 1 ) / 2, n1 + 2 - ( n2 - n1 ) % 2 );
   //////////////////////////////////////////////////
   //    lo1            lo1 + n1/2          lo1 + n1 - 1
   //     |                 |                       |
   //     X >>>>>>>>>>>>>>> X >>>>>>>>>>>>>>>>>>>>> X
   //                       |
   //                       m1
   //////////////////////////////////////////////////
   //                      mi2b
   //                       |
   // lo2 + n2 - 1    lo2 + n2 - 1 - n1/2
   //     |                 |
   //     Y <<<<<<<<<<<<<<< Y ...
   //                          .
   //                         .
   //                        .
   //                       .
   //                      .
   //                     .
   //                    .
   //                   ... Y <<<<<<<<<<<<<<< Y
   //                       |                 |
   //                       lo2 + (n1-1)/2    lo2
   //                       |
   //                       mi2a
   ///////////////////////////////////////////////////
   
   Rank mi1 = lo1 + n1 / 2;
   Rank mi2a = lo2 + ( n1 - 1 ) / 2;
   Rank mi2b = lo2 + n2 - 1 - n1 / 2;
   if ( S1[mi1] > S2[mi2b] ) //取S1左半、S2右半
      return median( S1, lo1, n1 / 2 + 1, S2, mi2a, n2 - ( n1 - 1 ) / 2 );
   else if ( S1[mi1] < S2[mi2a] ) //取S1右半、S2左半
      return median( S1, mi1, ( n1 + 1 ) / 2, S2, lo2, n2 - n1 / 2 );
   else //S1保留，S2左右同时缩短
      return median( S1, lo1, n1, S2, mi2a, n2 - ( n1 - 1 ) / 2 * 2 );
} //O(log(min(n1,n2))) ，可见实际上等长版本才是难度最大的
```


### 快速选取

## 希尔排序

### 排序大总结