## 8-2 Splay Tree 操作的分摊时间复杂度为 O (logn)
关于伸展树可在任意情况下均保持良好的操作效率，下图的实例还不足以作为严格的证明：
![[图08-07.伸展树中较深的节点一旦被访问到，对应分支的长度将随即减半.png]]

事实上，伸展树单次操作所需的时间量 T 起伏极大，并不能始终保证控制在 O(logn) 以内。故需从分摊的角度做一分析和评判。具体地，可将实际可能连续发生的一系列操作视作一个整体过程，将总体所需计算时间分摊至其间的每一操作，如此即可得到其单次操作的分摊复杂度 A，并依此评判伸展树的整体性能。

当然，就具体的某次操作而言，实际执行时间 T 与分摊执行时间 A 往往并不一致，如何弥合二者之间的差异呢？ 实际上，分摊分析法在教材中已经而且将会多次出现，比如此前第2.4.4节的可扩充向量、第5.4节的各种迭代式遍历算法以及后面第11.3.7节的 KMP 串匹配算法等。相对而言，伸展树的性能分析更为复杂，以下将采用**势能分析法**（potential analysis）。

仿照物理学的思想和概念，这里可假想式地认为，每棵伸展树 S 都具有一定量（非负）的势能（potential），记作Φ(S)。于是，若经过某一操作并相应地通过旋转完成伸展之后 S 演化为另一伸展树 S'，则对应的势能变化为：
$\Delta\Phi = \Phi(S')-\Phi(S)$ 

推而广之，考查对某伸展树 S0连续实施 m >> n 次操作的过程。将第 i 次操作后的伸展树记作 Si，则有：
$\Delta\Phi_{i} = \Phi(S_{i})-\Phi(S_{i-1})$ 

而从该过程的整体来看，应有
$\Delta\Phi =\sum\limits_{i=1}^{m} [\Phi(S_{i})-\Phi(S_{i-1})]=\Phi(S_{m})-\Phi(S_{0})$ 

也就是说，整体的势能变化量仅取决于最初和最终状态——这与物理学中势能场的规律吻合。势能函数与物理学中势能的另一相似之处在于，它也可以被看作是能量（计算成本）的一种存在形式。比如，==当某一步计算实际所需的时间小于分摊复杂度时，则可理解为通过势能的增加将提前支出的计算成本存储起来；反之，在前者大于后者时，则可从此前积累的势能中支取相应量用于支付超出的计算成本==。

以下，若将第 i 次操作的分摊复杂度取作实际复杂度与势能变化量之和，即 
$A=T_{i}+\Delta\Phi_{i}$

则有 $\sum\limits_{i=1}^{m}A_{i}=\sum\limits_{i=1}^{m}T_{i}+[\Phi(S_m)-\Phi(S_0)]$ 

如此，总体的实际运行时间 $\sum\limits_{i=1}^{m}T_{i}$，将不会超过总体的分摊运行时间 $\sum\limits_{i=1}^{m}A_{i}$，故后者可以视作前者的一个上界。

比如，R. E. Tarjan 使用如下势能函数：$\Phi(S)=\sum\limits_{v\in S}\log|v|$ , 其中|v| = 节点 v 的后代数目，证明了伸展树单次操作的分摊时间复杂度为 O(logn)。为此，以下将分三种情况（其余情况不过 是它们的对称形式）证明：
==在对节点 v 的伸展过程中，每一步调整所需时间均不超过 v 的势能变化的3倍，即：$3\cdot[\Phi'(v)-\Phi(v)]$ ==

**情况 A) zig** 
如此节 ![[60C-BBST-Splay-tree#zig/zag|双层伸展一节最后]]
所述，这种情况在伸展树的每次操作中至多发生一次，而且只能是伸展调整过程的最后一步。作为单旋，这一步调整实际所需时间为 T = O(1)。并且由图示可知，这步调整过程中只有节点 v 和 p 的势能有所变化，且 v（p）后代增加（减少）势能必上升（下降）， 故对应的分摊复杂度为：
$A=T+\Delta\Phi=1+\Delta\Phi(p)+\Delta\Phi(v)\le1+[\Phi'(v)-\Phi(v)]$ 

**情况 B) zig-zag**
作为双旋的组合，这一调整实际所需时间为 T = O(2)。
![[60C-BBST-Splay-tree#zig-zag/zag-zig]]

$$
\begin{aligned}
A &= T + \Delta\Phi \\
&= 2 + \Delta\Phi(v) + \Delta\Phi(p) + \Delta\Phi(g)\\
&= 2 + \Phi'(g) - \Phi(g) + \Phi'(p) - \Phi(p) + \Phi'(v) - \Phi(v)\\
&= 2 + \Phi'(g) + \Phi'(p) - \Phi(p) - \Phi(v)......... （∵ \Phi'(v) = \Phi(g)）\\
&\le 2 + \Phi'(g) + \Phi'(p) - 2\cdot\Phi(v) ............. （∵ \Phi(v) < \Phi(p)）\\
&\le 2 + 2\cdot\Phi'(v) - 2 - 2\cdot\Phi(v) . （∵ \Phi'(g) + \Phi'(p) \le 2\cdot\Phi'(v) - 2）\\
&= 2\cdot[\Phi'(v) - \Phi(v)]
\end{aligned}
$$

这里的最后一步放大，需利用对数函数 f(x) = log2 (x) 的性质，即该函数属于凹函数（concave function），因此必有：
$\frac{\log_{2}a+\log_{2}b}{2}\le\log_{2} \frac{a+b}{2}$ 亦即 

$\log_{2}a+\log_{2}b\le 2\cdot\log_{2} \frac{a+b}{2}=2\cdot[\log_{2}(a+b)-1]<2\cdot(\log_{2}c-1)$


**情况 C) zig-zig**
作为双旋的组合，这一调整实际所需时间也为 T = O(2)。
![[60C-BBST-Splay-tree#zig-zig/zag-zag]]
$$
\begin{aligned}
A &= T + \Delta\Phi \\
&= 2 + \Delta\Phi(v) + \Delta\Phi(p) + \Delta\Phi(g)\\
&= 2 + \Phi'(g) - \Phi(g) + \Phi'(p) - \Phi(p) + \Phi'(v) - \Phi(v)\\
&= 2 + \Phi'(g) + \Phi'(p) - \Phi(p) - \Phi(v)......... （∵ \Phi'(v) = \Phi(g)）\\
&\le 2 + \Phi'(g) + \Phi'(p) - 2\cdot\Phi(v) ............. （∵ \Phi(v) < \Phi(p)）\\
&\le2+\Phi'(g)+\Phi'(v)-2\cdot\Phi(v).............（∵ \Phi'(p) < \Phi'(v)）\\
&\le 3\cdot[\Phi'(v) - \Phi(v)].......... （∵ \Phi'(g) + \Phi(v) \le 2\cdot\Phi'(v) - 2）
\end{aligned}
$$

同样地，其中最后一步放大也需利用对数函数的凹性。

综合以上各种情况可知，无论具体过程如何，伸展操作的每一步至多需要 $3\cdot[\Phi'(v) - \Phi(v)]$ 时间。因此，若在对伸展树的某次操作中，节点 v 经过一连串这样的调整上升成为根节点 r，则整趟伸展操作总体所需的分摊时间为：

$A \le 1 + 3\cdot[\Phi (r) - \Phi (v)] \le 1 + 3\cdot\Phi(r) = O(1 + \log n) = O(\log n)$ 

## 8-4 证明 BTree 插入特定关键码确实需要 $\Omega(\log_{m}N)$ 次分裂
**描述**：试对于任何指定 m 和 N，构造一个存有 N 个关键码的 m 阶 BTree，使得在其中插入某特定关键码后需要 $\Omega(\log_{m}N)$ 次分裂。

不妨设 m 为奇数：
首先，考查由尽可能少的关键码组成的高度为 h 的 m 阶 B-树。 例如，如图 x8.1所示即是一棵高度 h = 4的 m = 5阶 B-树，其使用的关键码总数为： 2∙ $\lceil \frac{m}{2}\rceil^{h-1}$ - 1 = 53
![[61B-Exercise-8-4.png]]

考查该树的最右侧通路。因该通路在图中以粗线条和黑色方格示意，故不妨将沿途的关键码称作黑关键码，其余称作白关键码。于是，如阴影虚框所示，可以将整棵树分割为一系列的子树。

进一步地，如此划分出来的子树，可与最右侧通路上的关键码建立起一一对应的关系：每棵子树的直接后继都是一个黑关键码——亦即不小于该子树的最小关键码。当然特别地，最右侧通路末端节点中的关键码可视作空树的直接后继。 不妨设此树所存的关键码为：{ 1, 2, ..., n } 以下，若从 n + 1 起，按递增次序继续插入关键码，**则只能沿最右侧通路发生分裂**。而且，在根节点保持只有单个关键码的前提下，全树的高度必然保持不变。考查如此所能得到的规模最大的 B-树，除根节点外，其最右侧通路上各节点都应含有 m - 1个关键码（处于饱和状态）。这样的一个实例，如图 x8.2 所示。

![[61B-Exercise-8-4-2.png]]

若将黑、白关键码所属的节点，亦分别称作黑节点、白节点，则此时它们应分别处于上溢和下溢的临界状态。接下来若再插入一个关键码，而且大于目前已有的所有关键码，则必然会沿着最右侧通路（持续）发生 h - 1次分裂。

为统计该树的规模，依然如图中阴影虚框所示，沿着最右侧通路将所有节点分组。进一步地，如此划分出来的子树，同样与最右侧通路上的黑关键码一一对应。以下，我们将每棵子树与对应的黑关键码归为一组。如此划分之后，考查其中高度为 k 的任一子树所属的分组，不难发现其规模应为：$\lceil \frac{m}{2}\rceil^{k}$

因此，全树的总规模应为：
$$
\begin{aligned}
\hat{N}&=\lceil\frac{m}{2}\rceil^{h-1}+(m-1)\cdot[\lceil\frac{m}{2}\rceil^{h-2}+\lceil\frac{m}{2}\rceil^{h-3}+...+\lceil\frac{m}{2}\rceil^{0})\\
&=\left[\lceil\frac{m}{2}\rceil^{h-1}\cdot\left(m+\lceil\frac{m}{2}\rceil-2\right)-m+1\right] / (\lceil\frac{m}{2}\rceil-1)....(*)
\end{aligned}
$$
反之，便有：
$$
\begin{aligned}
h&=1+\log_{\lceil\frac{m}{2}\rceil} [((\lceil\frac{m}{2}\rceil-1)\cdot\hat{N}+m-1)/(m+\lceil\frac{m}{2}\rceil-2)]\\
&=\Theta(\log_{\lceil\frac{m}{2}\rceil}\hat{N})=\Theta(\log_{m}\hat{N})
\end{aligned}
$$

因此，对于任意指定的规模 N，若令：
$h=1+\lfloor\log_{\lceil\frac{m}{2}\rceil}[((\lceil\frac{m}{2}\rceil-1)\cdot N +m-1)/(m+\lceil\frac{m}{2}\rceil-2)]\rfloor$ 
并按 ( * ) 式估算出 $\hat{N}\le N$，则可按上述方法构造一棵高度为 h、规模为 $\hat{N}$ 的 m 阶 BTree，且接下来只要再插入一个全局最大关键码，就会沿最右侧通路发生 $h - 1 = \Omega(\log_{m}\hat{N})$ 次分裂。而其余 $N - \hat{N}$ 个 关键码，可在不影响最右侧通路的前提下，作为白关键码适当地插入并散布到各棵子树当中。

## 8-5 何种次序插入关键码使 BTree 高度最大
**描述**：一组 n 个互异关键码，插入一棵初始空的 m 阶 BTree，且 m << n。

保证 B-树达到最大高度的一种简明方法，就是**按单调次序插入所有关键码**。

不妨设 m 为奇数。比如，按单调递增次序将： { 0, 1, 2, ..., 51 } 插入初始为空的5阶 B-树，所生成 B-树的结构应如图 x8.3所示。

![[61B-Exercise-8-5.png]]

一般地，不难验证：在按递增次序插入各关键码的过程中，最右侧通路（沿途节点在图中以黑色示意）以下的所有子树（以虚框包围的各组白色节点），始终都属于“稀疏临界”状态。在处于这种状态的子树中，任一节点的删除，都将引起持续的合并操作，并导致(子树)高度的下降。

因此，若阶次为 m，则此类子树中的每个节点均有 $\lceil\frac{m}{2}\rceil$ 分支；若其高度为 h，则其下所含的外部节点总数应为 $\lceil\frac{m}{2}\rceil^h$，内部节点总数应为 $\lceil\frac{m}{2}\rceil^{h}-1$。在上例中 m = 5，于是高度为 h = 1 的（4棵）此类子树必然包含3个外部节点和2个内部节点，高度为 h = 2的（4棵）此类子树必然包含9个外部节点和8个内部节点。

实际上若采用单调递增的次序，则每次插入的关键码在当前都属最大。因此，插入算法必然沿着最右侧通路做查找并确定其插入位置；而一旦出现上溢现象，也只能沿最右侧通路实施分裂操作。如此，尽管最右侧通路下属的子树可能会增加，但它们==始终保持稀疏临界状态==。 

一般地，仿照教材8.2.4节的分析方法可知：如此插入\[0, n)而生成的 m 阶 B-树，高度应为：
$h = h_{max} = \log_{\lceil\frac{m}{2}\rceil}  \lfloor\frac{n+1}{2}\rfloor + 1$ 

仍以上述 B-树为例，m = 5，n = 52，故树高应为：
$h = \log_{\lceil\frac{5}{2}\rceil}  \lfloor\frac{52+1}{2}\rfloor + 1=3$ 

若继续插入下一关键码52，则在持续分裂3次之后，树高将增至：
$h = \log_{\lceil\frac{5}{2}\rceil}  \lfloor\frac{53+1}{2}\rfloor + 1=4$ 依然是此时所能达到的最大树高。

## 8-5-b 何种插入关键码的次序使 BTree 高度最小
经过思考 8-5 的过程及实践，发现只要插入次序不是单调的，就会导致 BTree 高度降低，并且当插入顺序是满足——插满一个节点，换到下一个节点——就会使 BTree 高度最小。

但是这种规律并不好找，且实现麻烦，尝试以随机序列插入：
- 以顺序插入 1~30 关键码：
	- ![[61B-Exercise-insert-BTree-sequential.png]]
- 以随机次序插入 1~30 关键码：
	- \[19, 7, 3, 1, 25, 9, 20, 5, 15, 14, 23, 27, 2, 21, 6, 30, 24, 29, 22, 12, 8, 26 , 17, 16, 11, 18, 4, 28, 13, 10](randomized by python)
	- ![[61B-Exercise-python-randomize.png]]
	- ![[61B-Exercise-insert-BTree-random.png]]

More discussion：[algorithm - In what order should you insert a set of known keys into a B-Tree to get minimal height? - Stack Overflow](https://stackoverflow.com/questions/16001727/in-what-order-should-you-insert-a-set-of-known-keys-into-a-b-tree-to-get-minimal) 

其中第一个回答的思路比较有趣：
The following trick should work for most ordered search trees, assuming the data to insert are the integers `1..n`.

Consider the binary representation of your integer keys - for 1..7 (with dots for zeros) that's...

```
Bit : 210
  1 : ..1
  2 : .1.
  3 : .11
  4 : 1..
  5 : 1.1
  6 : 11.
  7 : 111
```

Bit 2 changes least often, Bit 0 changes most often. That's the opposite of what we want, so what if we reverse the order of those bits, then sort our keys in order of this bit-reversed value...

```
Bit : 210    Rev
  4 : 1.. -> ..1 : 1
  ------------------
  2 : .1. -> .1. : 2
  6 : 11. -> .11 : 3
  ------------------
  1 : ..1 -> 1.. : 4
  5 : 1.1 -> 1.1 : 5
  3 : .11 -> 11. : 6
  7 : 111 -> 111 : 7
```

It's easiest to explain this in terms of an unbalanced binary search tree, growing by adding leaves. The first item is dead centre - it's exactly the item we want for the root. Then we add the keys for the next layer down. Finally, we add the leaf layer. At every step, the tree is as balanced as it can be, so even if you happen to be building an AVL or red-black balanced tree, the rebalancing logic should never be invoked.

\[**EDIT** I just realised you don't need to sort the data based on those bit-reversed values in order to access the keys in that order. The trick to that is to notice that bit-reversing is its own inverse. As well as mapping keys to positions, it maps positions to keys. So if you loop through from 1..n, you can use this bit-reversed value to decide which item to insert next - for the first insert use the 4th item, for the second insert use the second item and so on. One complication - you have to round n upwards to one less than a power of two (7 is OK, but use 15 instead of 8) and you have to bounds-check the bit-reversed values. The reason is that bit-reversing can move some in-bounds positions out-of-bounds and visa versa.]

Actually, for a red-black tree _some_ rebalancing logic will be invoked, but it should just be re-colouring nodes - not rearranging them. However, I haven't double checked, so don't rely on this claim.

For a B tree, the height of the tree grows by adding a new root. Proving this works is, therefore, a little awkward (and it may require a more careful node-splitting than a B tree normally requires) but the basic idea is the same. Although rebalancing occurs, it occurs in a balanced way because of the order of inserts.

This can be generalised for any set of known-in-advance keys because, once the keys are sorted, you can assign suitable indexes based on that sorted order.

**WARNING** - This isn't an efficient way to construct a perfectly balanced tree from known already-sorted data.

If you have your data already sorted, and know it's size, you can build a perfectly balanced tree in O(n) time. Here's some pseudocode...

```
if size is zero, return null
from the size, decide which index should be the (subtree) root
recurse for the left subtree, giving that index as the size (assuming 0 is a valid index)
take the next item to build the (subtree) root
recurse for the right subtree, giving (size - (index + 1)) as the size
add the left and right subtree results as the child pointers
return the new (subtree) root
```

Basically, this decides the structure of the tree based on the size and traverses that structure, building the actual nodes along the way. It shouldn't be too hard to adapt it for B Trees.

## 8-6 考查 BTree 节点插入导致的分裂次数
1. ==对任意阶 BTree T，若 T 的初始高度为 1，在经历连续若干次插入操作后，高度增加至 h 且共有 n 个内部节点，则在此过程中 T 总共分裂多少次==？

考查因新关键码的插入而引起的任何一次分裂操作。被分裂的节点，无非两种类型：
1. 若它不是根节点，则树中的节点增加一个，同时树高保持不变，故有： n += 1 和 h += 0 
2. 否则若是根节点，则除了原节点一分为二，还会新生出一个（仅含单关键码的）树根，同时树的高度也将相应地增加一层，故有： n += 2 和 h += 1 

可见，无论如何，n 与 h 的差值均会恰好地增加一个单位——因此，n - h 可以视作为分裂操作的一个计数器。该计数器的初始值为1 - 1 = 0，故最终的 n - h 即是从初始状态之最后，整个过程中所做分裂操作的总次数。

请注意，==以上结论与各关键码的数值大小以及具体的插入过程均无关，仅取决于 B-树最初和最终的状态——高度和内部节点数==。


2. ==在上述过程中，每一关键码的插入，平均引发了多少次分裂操作==？
由上可见，累计发生的分裂操作次数，不仅取决于连续插入操作的次数，同时也取决于最终的树高。前者亦即树中最终所含关键码的总数 N，后者即是 h。

若关键码总数固定为 N，则为使节点尽可能地多，内部节点各自所含的关键码应尽可能地少。注意到根节点至少包含1个关键码，其余内部节点至少包含 $\lceil\frac{m}{2}\rceil-1$ 个关键码，故必有： $n \le 1 + (N - 1) / (\lceil\frac{m}{2}\rceil-1)$ 

因此，在如上连续的 N 次插入操作中，分裂操作的平均次数必然不超过： $\frac{n - h}{N} < \frac{n}{N} < \frac{1}{\lceil\frac{m}{2}\rceil-1}$ 

可见，平均而言，大致每经过 $\lceil\frac{m}{2}\rceil-1$ 次插入，才会发生一次分裂。

根据习题8-4的结论，某一关键码的插入，在最坏情况下可能引发多达 $\Omega(\log_{m}N)$ 次的分 裂。对照本题的结论可知，这类最坏情况发生的概率实际上极低。

3. ==若 T 的初始高度为 h 且含有 n 个内部节点，而在经过连续若干次删除操作后高度下降至 1，则此过程中 T 总共合并过多少次==？
与 1) 同理，若合并后的节点不是树根，则有 n -= 1 和 h -= 0 , 否则若是根节点，则有： n -= 2 和 h -= 1 可见，无论如何，n 与 h 的差值 n - h 均会恰好地减少一个单位。

既然最终有： n = h = 1 或等价地 n - h = 0 故其间所发生合并操作的次数，应恰好等于 n - h 的初值。

同样请注意，以上结论与各关键码的数值大小以及具体的删除过程均无关，仅取决于 B-树 最初和最终的状态——高度和内部节点数。

4. ==设 T 的初始高度为 1，而且在随后经过若干次插入和删除操作——次序随意，且可能彼此相同。试证明：若在此期间总共做过 S 次分裂和 M 次合并，且最终共有 n 个内部节点、高度为 h，则必有 S-M=n-h==
综合 1) 和 3) 的结论可知：在 B-树的整个生命期内，n - h 始终忠实反映了分裂操作次数与合并操作次数之差。 

需要特别说明的是，以上前三问只讨论了连续插入和连续删除的情况，其结论并不适用于本问的情况——两种操作可以任意次序执行。下题将要考查的，即是其中的极端情况。

## 8-7 BTree 反复插入、删除操作导致结构的变化
**描述**：==构造一棵高度 h 的 m（m>=3, odd） 阶 BTree 使得反复交替地对其插入、删除操作，每次插入或删除都会引发 h 次分裂或合并==。

若从一棵空的 m 节 B-树开始，按单调顺序依次插入以下关键码： { 1, 2, 3, 4, 5, ..., N }, 其中，N = 2∙\[((m + 1)/2)^h - 1]

则易见，树高恰好为 h，而且最右侧通路上的节点均有 m 个分支，其余节点各有(m + 1)/2个分支。

于是，接下来若继续插入关键码 N + 1，则会沿最右侧通路发生 h 次分裂，全树增高一层；接下来若再删除关键码 N + 1，则会沿着最右侧通路发生 h 次合并，全树降低一层。

更重要的是，如此经过一轮插入和删除，该树宏观的结构以及各节点的组成，都将完全复原。==这就意味着，若反复地如此交替地插入和删除，则每一次操作都会在该树中引发 h 处结构性改变==。 当然，此类最坏情况在实际应用中出现的概率同样极低，平均而言，B-树节点分裂与合 的次数依然极少。

## 8-8 旋转修复上溢
**描述**：对比本章所介绍的 B-树插入与删除算法后不难发现，两者并不完全对称。 比如，删除关键码时若发生下溢，则可能采用旋转（通过父亲间接地向兄弟借得一个关键码） 或者合并两种手段进行修复；然而，插入关键码时若发生上溢，却只是统一通过分裂进行修复。 实际上从理论上讲，也可优先通过旋转来修复上溢： 只要某个兄弟仍处于非饱和状态，即可通过父亲，间接地向该兄弟借出一个关键码。

==为什么不倾向于采用这种手段，而是直接通过分裂来修复上溢==？

表面上看，B-树的插入操作与删除操作方向相反、过程互逆，但二者并非简单的对称关系。 在删除操作的过程中若当前节点发生下溢，未必能够通过合并予以修复——除非其兄弟节点亦处于下溢的临界状态。

而在插入操作的过程中若当前节点发生上溢，则无论其兄弟节点的状态和规模如何，总是可以立即对其实施分裂操作。实际上就算法的控制逻辑而言，优先进行分裂更为简明。

而根据习题8-6的分析结论，在 B-树的生命期内，分裂操作通常都不致过于频繁地发生。因此，不妨直接采用优先进行分裂的策略来修复上溢节点。 另外，优先进行分裂也不致于导致空间利用率的显著下降。实际上无论分裂多少次，无论分裂出多少个节点，根据 B-树的定义，其空间利用率最差也不致低于50%。

最后，优先分裂策略也不致于导致树高——决定 I/O 负担以及访问效率的主要因素——的明显增加。实际上根据教材8.2.4节的分析结论，B-树的高度主要取决于所存关键码的总数，而与其中节点的数目几乎没有关系。

## 8-9 B* 树

## 8-11 半平衡二叉搜索树

## 8-12 B 树存放若干关键码后最大、最小高度

## 8-13 考察红黑树高度

## 8-14 考察红黑树重染色节点的数量

