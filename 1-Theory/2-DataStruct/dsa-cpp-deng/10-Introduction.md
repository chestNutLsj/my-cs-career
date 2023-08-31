## Algorithm
$$
Algorithm + Data\ Structure=Program
$$

$$
(Algo+DS)\times Efficiency=Computation
$$

## 时间复杂度
### T-notation
$$
T_A(P)=算法A求解问题P的计算成本
$$
$$
T_A(n)=算法A求解问题规模为n的计算成本
$$
A 在上下文确定时，可以省略。

$$
T(n)=max\{T(P)\ |\ |P|=n\}
$$
只关注成本最高者。
### big-O-notation
渐进分析 (Asymptotic analysis)：只关心问题规模足够大（$n\rightarrow \infty$）后，计算成本的增长趋势。

**大 O 记号**：
$$
T(n)=O(f(n))
$$
当且仅当，$\forall n\gg 2,\exists c>0,使得\ T(n)<c\cdot f(n)$

推论：
1. $n\gg2时，O(f(n))=O(c\cdot f(n))$;
2. $O(n^{a}+n^{b})=O(n^{a}),a\ge b> 0$;
3. $\lim_{n\rightarrow\infty} \frac{T(n)}{f(n)}$ 不一定存在;

举例：
$$
\begin{align*}
&\sqrt{5n[3n(n+2)+4]+6} \\
&< \sqrt{5n[6n^2+4]+6} \\
&< \sqrt{35n^{3+6}} \\
&< 6 \cdot n^{1.5} \\
&= O(n^{1.5})
\end{align*}
$$

$O(f(n))$ 是 $T(n)$ 的上界渐进，作为 n 充分大时对 $T(n)$ 的悲观估计。

### Ω-notation
当且仅当 $\forall n\gg2,\exists c>0,使得 T(n)>c\cdot f(n)$，记为：
$$
T(n)=\Omega(f(n))
$$

$\Omega(f(n))$ 是 $T(n)$ 的下界渐进，作为 n 充分大时对 $T(n)$ 的乐观估计。

### Θ-notation

当且仅当 $\forall n\gg2,\exists c_{1}>c_{2}>0,使得 c_{1}\cdot f(n)>T(n)>c_{2}\cdot f(n)$，记为：
$$
T(n)=\Theta(f(n))
$$

$\Theta(f(n))$ 是 $T(n)$ 的确界，作为 n 充分大时对 $T(n)$ 渐近紧致的估计。

![[图01-04.大O记号、大Omega记号和大theda记号.png]]

### 大 O 记号的多项式
#### $O (1)$: constant

#### $O (logn)$: ploy-log
- 底数无所谓；
- 常系数无所谓；
- 常数次幂无所谓：$\forall c>0,logn^{c}=c\cdot logn=\Theta(logn)$;
	- 举例：
$$
\begin{equation}
\begin{aligned}
123\cdot \log^{321}n &+ \log^{205}(7\cdot n^{2}-15n+31) \\
&< 123\cdot \log^{321}n + \log^{205}(8n^{2}) \\
&< 123\cdot \log^{321}n + 2\cdot \log^{205}(3n) \\
&= \Theta(\log^{321}n)
\end{aligned}
\end{equation}
$$
- 复杂度接近常数：$\forall c>0,logn=O(n^c)$;
	- $\lim_{n\rightarrow \infty} \frac{ln(n)}{n^{c}}=0$;


#### $O (n^c)$: polynomial
$a_{k}\cdot n^{k}+a_{k-1}\cdot n^{k-1}+\cdots+a_{2}\cdot n^{2}+a_{1}\cdot n^{1}+a_{0}=O(n^{k}),a_{k}>0$

#### $O (2^n)$: exponential
无穷级数表示指数： $e^n=1+n+\frac{n^{2}}{2!}+\frac{n^{3}}{3!}+\dots$
因而 $\forall c>1,n^{c}<e^{n}=O(2^{n})$ 

$O(n^{c})$ 到 $O(2^{n})$ 是有效算法到无效算法的分水岭。

**NPC 问题**：No-Polynomial complete
典型如 2-Subset 问题，其有定理 $|2^{S}|=2^{|S|}=2^{n}$

![[算法复杂度层次级别.png]]
- $log^{*}n$ 表示对数迭代函数，即取多少次对数，使得 $logn$ 的值小于等于 1，返回的是对数运算的次数；
	- $log^{*}_{2}16=3$
	- $log^{*}_{2}2^{1024}=5:$
		- $log_{2}2^{1024}=1024$,
		- $log_{2}1024=10$,
		- $log_{2}10\approx 3.2$,
		- $log_{2}3.2\approx 1.8$,
		- $log_{2}1.8<1$
	- $log^{*}n=O(log^{*}(logn))$ 

## 复杂度分析
### 级数

算术级数：与末项平方同阶
- $T(n)=1+2+...+n=\binom{n+1}{2}=\frac{n(n+1)}{2}=O(n^{2})$

幂方级数：比幂级数高出一阶
- $T(n)=\sum_{k=0}^{n}k^{d}\approx \int_{0}^{n}x^{d}dx=\frac{n^{d+1}}{d+1}=O(n^{d+1})$

几何级数：与末项同阶
- $T_{a}(n)=\sum_{k=0}^{n}=a^{0}+a^{1}+...+a^{n}=\frac{a^{n+1}-1}{a-1}=O(a^{n}),1<a$
- 即等比数列求和


**收敛**：
收敛级数：$O(1)$
- $\sum\limits_{k=2}^{n}\frac{1}{(k-1)\cdot k}=\frac{1}{1\cdot 2}+\frac{1}{2\cdot 3}+...+\frac{1}{(n-1)\cdot n}=1-\frac{1}{n}=O(1)$
- $\sum\limits_{k\ is\ a\ perfect\ power}\frac{1}{k-1}=\frac{1}{3}+\frac{1}{7}+\frac{1}{8}+\frac{1}{15}+...=1=O(1)$

几何分布: $O(1)$
- $(1-\lambda)\cdot[1+2\lambda+3\lambda^{2}+4\lambda^{3}+...]=\frac{1}{1-\lambda}=O(1),0<\lambda<1$
- 举例：对于一个抛硬币实验，正面向上概率为 $\lambda$，则抛若干次至第一次出现正面向上的期望为 $(1-\lambda)\cdot[1+2\lambda+3\lambda^{2}+4\lambda^{3}+...]=\frac{1}{1-\lambda}=O(1),0<\lambda<1$


**不收敛，但有限**：
调和级数：$\Theta(logn)$
- $h(n)=\sum\limits_{k=1}^{n}\frac{1}{k}=1+\frac{1}{2}+\frac{1}{3}+...=lnn+\gamma+O(\frac{1}{2n})=\Theta(logn)$

对数级数：$\Theta(nlogn)$
- $\sum\limits_{k=1}^{n}lnk=ln\prod\limits_{k=1}^{n}=ln(n!)\approx (n+0.5)lnn-n=\Theta(nlogn)$
- stirling formulation: $n!\approx \sqrt{2\pi n}\cdot (\frac{n}{e})^n$ 
- stirling approximation: $ln(n!)=n\ln n-n+O(\ln n)$ 

### 迭代
![[涉及级数的迭代时间复杂度估计.png]]

### 习题集中例题结论

#### 1-9 log n = O(n^ε)
对任何 $ε>0$，都有 $\log n=O(n^{ε})$

**证明**：
- 函数 $\ln n$ 增长得极慢，故总存在 $M > 0$，使得 $n > M$ 之后总有 $\ln n < εn$。
- 令 $N = e^M$，则当 $n > N$（即 $\ln n > M$）之后，总有：$\ln(\ln n) < ε\ln n$，亦即：$\ln n < n^ε$。

#### 1-10 等差等比之和的 O-notation
**等差级数之和与其中最大项的平方同阶**：
- 考查首项为常数 x、公差为常数 d > 0、长度为 n 的等差级数： $\{ x, x + d, x + 2d, ..., x + (n - 1)d \}$ 
- 其中末项 $(n - 1)d = \Theta(n)$，各项总和为： $(\frac{d}{2})n^{2} + (x - \frac{d}{2})n = \Theta(n^{2} )$。

**等比级数之和与最大项同阶**：
- 考查首项为常数 x、公比为常数 d > 1、长度为 n 的等比级数： $\{ x, xd, xd^{2} , ..., xd^{n-1} \}$ 
- 其中末项 $xd^{n-1} = \Theta(d^{n} )$，各项总和为： $x(d^{n} -1)/(d-1) = \Theta(d^{n} )$

#### 算法导论中排序渐进增长率的题
![[算法导论中排序渐进增长率.png]]

## 迭代与递归
### 减治
![[减治.png]]

#### 线性递归跟踪
![[递归跟踪.png]]
- 跟踪递归发生了多少次，每次递归占用了多少空间、运行了多少时间，即可计算整体时间复杂度
- 该问题中空间复杂度为 $O(n)$；

#### 线性递推方程
![[递推方程.png]]
- 减治的思想是，每一次操作都将问题规模减小一部分，被减小的平凡部分时间复杂度是固定的 $O(1)$ 或其它，然后递推下去；

### 分治
![[分治.png]]
- 二分查找是减治，归并排序是分治；
- 减治思想是除去平凡的子问题；而分治思想是将原问题切分成若干子问题，对子问题进行同样的操作，直到子问题变为平凡；

#### 二分递归跟踪
![[分治sum.png]]

![[二分递归跟踪.png]]
- sum (0,8)先入栈，接着是 sum (0,4)，接着是 sum (0,2)，最后是 sum (0,1)，此时栈顶为平凡地返回 A[0]，出栈后 sum (1,2)入栈得到 A[1]，出栈后栈顶变为 sum (0,2)则得到 sum (0,1)+sum (1,2)=A[0]+A[1]，以此类推；
- 递归深度为 $\log n$；
#### 二分递推方程

$$
\begin{aligned}
&T(n)=2\cdot T(\frac{n}{2})+O(1) \\
&...\\
&T(1)=O(1) //递归基:sum(A,k,k)\\
\end{aligned}
$$

求解：

$$
\begin{aligned}
T(n)&=2\cdot T(\frac{n}{2})+O(1) \\
&=4\cdot T(\frac{n}{4})+O(3) \\
&=8\cdot T(\frac{n}{8})+O(7)\\
&...\\
&=n\cdot T(1)+O(n-1)\\
&=O(2n-1)\\
&=O(n)
\end{aligned}
$$

### 主定理
![[主定理.png]]

Master Theorem 是一种用于分析递归算法时间复杂度的工具，特别适用于分治算法中的递归情况。它能够帮助我们确定一类递归算法的时间复杂度的渐近界限。

Master Theorem的一般形式如下：

如果递归算法的递归式可以表示为：$T(n) = aT(\frac{n}{b}) + f(n)$，其中：
- a 是递归调用的次数
- b 是问题规模缩小的比例（通常是大于1的正整数）
- f(n) 是除了递归调用以外剩余操作的时间复杂度

那么，如果存在常数 c 和 k ≥ 0，使得 f(n) = O(n^k) 成立，那么递归算法的时间复杂度 T(n) 可以通过以下三种情况来确定：

1. 如果 f(n) = Θ(n^k)，那么 T(n) = Θ(n^k * log n)。
    
2. 如果 f(n) = Θ(n^k * log^p n)，其中 p ≥ 0，那么 T(n) = Θ(n^k * log^(p+1) n)。
    
3. 如果 f(n) = Θ(n^c)，其中 c > k，且存在 ε > 0 和 d < 1，使得 a_f(n/b) ≤ d_f(n)，那么 T(n) = Θ(f(n))。
    

需要注意的是，Master Theorem只适用于特定类型的递归式，即满足上述形式的递归式。如果递归式不符合这个形式，Master Theorem就不能直接应用。

在使用Master Theorem时，需要首先将递归式进行分类，然后根据分类结果来确定时间复杂度的上界。这个定理在分析一些常见的分治算法（如二分查找、归并排序等）的时间复杂度时非常有用。