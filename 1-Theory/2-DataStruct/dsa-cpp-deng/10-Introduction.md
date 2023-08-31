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

### 习题集结论

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

### 判断题：关于减治和分治的区分
**判断**: 用分治思想来解决长度为 n 的数组求和问题 (n 足够大), 递归实例的数目会比用减治的方法少. (❌)

**解析**: 
- 减冶算法中递归实例分别是: 1 个规模为 n 的实例, 1 个规模为 n-1 的实例, 1 个规模为 n-2 的实例, … 共 n 个；
- 分治算法中, 递归实例分别是: 1 个规模为 n 的实例, 2 个规模为 $\frac{n}{2}$ 的实例, 4 个规模为 $\frac{n}{4}$ 的实例, 共有 1 + 2 + 4 + … + n 个。

## 主定理
![[主定理.png]]

### Generic form
Master Theorem 是一种用于分析递归算法时间复杂度的工具，特别适用于分治算法中的递归情况。这些算法将输入划分为大小相等的较小子问题，递归求解子问题，然后组合子问题解决方案以给出原始问题的解决方案。这种算法的时间可以通过将它们在递归的顶层执行的工作（将问题划分为子问题，然后组合子问题解决方案）与算法的递归调用中所做的时间相加来表示。

Master Theorem的一般形式如下：

如果递归算法的递归式可以表示为：$T(n) = aT(\frac{n}{b}) + f(n)$，其中：
- $a$ is the number of subproblems in the recursion,
- $b$ is the factor by which the subproblem size is reduced in each recursive call (b>1), (a b 不能与 n 相关)
- $f(n)$ denotes the amount of time taken at the top level of the recurrence.

Recurrences of this form often satisfy one of the three following regimes, based on how the work to split/recombine the problem $f(n)$ relates to the critical exponent 
$$c_{crit}=log_{b}a=\frac{log(subproblems num)}{log(relative subproblem size)} $$.
#### Case 1
Work to split/recombine a problem is dwarfed by subproblems.
- 如果 $f(n) = O(n^{c}),c<c_{crit}$，那么 $T(n) = Θ(n^{c_{crit}})$
- The splitting term does not appear; the recursive tree structure dominates.

#### Case 2
Work to split/recombine a problem is comparable to subproblems.
- 如果 $f(n) = Θ(n^{c_{crit}} * \log^{k} n),k\ge0$，那么 $T(n) = Θ(n^{c_{crit}} * log^{(k+1)} n)$
- rangebound by the critical-exponent polynomial, times zero or more optional $\log$ s,
- The bound is the splitting term, where the log is augmented by a single power.

#### Case 3
Work to split/recombine a problem dominates subproblems.
- 如果 $f(n) = Ω(n^c),c>c_{crit}$，且存在 $k< 1$，使得 $af(\frac{n}{b}) ≤ k{f(n)}$，那么 $T(n) = Θ(f(n))$。


### Example
#### Case 1：
$$T(n)=8T\left({\frac {n}{2}}\right)+1000n^{2}$$

As one can see from the formula above:
$$a=8,\,b=2,\,f(n)=1000n^{2}$$, so
$$f(n) = O\left(n^c\right)$$, where $c=2$.

Next, we see if we satisfy the case 1 condition:
$$\log _{b}a=\log _{2}8=3>c$$
It follows from the first case of the master theorem that
$$T(n) = \Theta\left( n^{\log_b a} \right) = \Theta\left( n^{3} \right)$$

(This result is confirmed by the exact solution of the recurrence relation, which is $T(n)=1001n^{3}-1000n^{2}$, assuming $T(1)=1$).

#### Case 2
$$T (n)=2T\left({\frac {n}{2}}\right)+10n$$
As we can see in the formula above the variables get the following values:
$$a=2,\,b=2,\,c=1,\,f(n)=10n $$
$$f(n)=\Theta \left(n^{c}\log ^{k}n\right) $$ where $c=1,k=0$.

Next, we see if we satisfy the case 2 condition:
$$\log _{b}a=\log _{2}2=1 $$ and therefore, c and $\displaystyle \log _{b}a$ are equal.

So it follows from the second case of the master theorem:

$$T(n)=\Theta \left(n^{\log _{b}a}\log ^{k+1}n\right)=\Theta \left(n^{1}\log ^{1}n\right)=\Theta \left(n\log n\right) $$

Thus the given recurrence relation $T(n)$ was in $\Theta(n \log n)$.

(This result is confirmed by the exact solution of the recurrence relation, which is $T(n)=n+10n\log _{2}n$, assuming $T(1)=1$).

#### Case 3
$$T(n)=2T\left({\frac {n}{2}}\right)+n^{2} $$
As we can see in the formula above the variables get the following values:
$$a=2,\,b=2,\,f(n)=n^{2} $$
$$f(n)=\Omega \left(n^{c}\right) $$, where $c=2$.

Next, we see if we satisfy the case 3 condition:
$$\log _{b}a=\log _{2}2=1 $$, and therefore, yes, $$c>\log _{b}a $$
The regularity condition also holds:
$$2\left({\frac {n^{2}}{4}}\right)\leq kn^{2} $$, choosing $k=\frac{1}{2}$.

So it follows from the third case of the master theorem:
$$T\left(n\right)=\Theta \left(f(n)\right)=\Theta \left(n^{2}\right) $$
Thus the given recurrence relation $T(n)$ was in $\Theta (n^{2})$, that complies with the $f(n)$ of the original formula.

(This result is confirmed by the exact solution of the recurrence relation, which is $T(n)=2n^{2}-n$, assuming $T(1)=1$)

### Inadmissible equations
The following equations cannot be solved using the master theorem:

- $T(n)=2^{n}T\left({\frac {n}{2}}\right)+n^{n}$
    _a_ is not a constant; the number of subproblems should be fixed.

- $T(n)=2T\left({\frac {n}{2}}\right)+{\frac {n}{\log n}}$
    non-polynomial difference between $f(n)$ and $n^{\log _{b}a}$ (see below; extended version applies)

- $T(n)=0.5T\left({\frac {n}{2}}\right)+n$
    ${\displaystyle a<1}$ cannot have less than one sub problem.
    
- $T(n)=64T\left({\frac {n}{8}}\right)-n^{2}\log n$
    $f(n)$, which is the combination time, is not positive

- $T(n)=T\left({\frac {n}{2}}\right)+n(2-\cos n)$
    case 3 but regularity violation.


In the second inadmissible example above, the difference between $f(n)$ and $n^{\log _{b}a}$ can be expressed with the ratio ${\displaystyle {\frac {f(n)}{n^{\log _{b}a}}}={\frac {n/\log n}{n^{\log _{2}2}}}={\frac {n}{n\log n}}={\frac {1}{\log n}}}$. It is clear that ${\frac {1}{\log n}}<n^{\epsilon }$ for any constant $\epsilon >0$. Therefore, the difference is not polynomial and the basic form of the Master Theorem does not apply. The extended form (case 2b) does apply, giving the solution ${\displaystyle T(n)=\Theta (n\log \log n)}$.

### Application
| Algo                  | Recurrence relationship     | Run time     | Comment                                                           |
| --------------------- | --------------------------- | ------------ | ----------------------------------------------------------------- |
| Binary Search         | $T(n)=T(\frac{n}{2})+O(1)$  | $O(\log n)$  | Apply Master theorem case $c=\log _{b}a$, where $a=1,b=2,c=0,k=0$ |
| Binary Tree Traversal | $T(n)=2T(\frac{n}{2})+O(1)$ | $O(n)$       | Apply Master theorem case $c<\log_{b}a$ where $a=2,b=2,c=0$       |
| Merge Sort            | $T(n)=2T(\frac{n}{2})+O(n)$ | $O(n\log n)$ | Apply Master theorem case $c=\log_{b}a$ where $a=2,b=2,c=1,k=0$   |
## 迭代与递归应用：总和最大区段

从整数数列中找出总和最大的区段（有多个时，短者、靠后者优先）。

### 蛮力
![[10-Introduction-gsBF.png]]


### 递增策略
![[10-Introduction-gsIC.png]]
1. 首先，代码定义了一个变量 `gs`，用于存储当前已知的最大和，初始值为序列的第一个元素 `A[0]`。
2. 外层循环使用变量 `i` 枚举所有可能的起始位置。这个循环将从序列的第一个元素开始，依次遍历到序列的最后一个元素。
3. 在内层循环中，使用变量 `j` 从当前起始位置 `i` 开始遍历到序列的末尾。在这个循环中，定义了一个变量 `s` 用于累计当前区段的和。
4. 对于每个 `j`，将序列中的元素 `A[j]` 加到变量 `s` 中，从而递增地计算当前区段的总和。这个操作的时间复杂度是 O(1)，因为每次只需进行一次加法。
5. 在每次计算完区段的和后，代码比较 `gs`（当前已知的最大和）与 `s`（当前区段的和）。如果 `s` 更大，则更新 `gs` 为 `s`，从而保持 `gs` 始终存储最大的连续区段和。
6. 继续循环，内层循环将覆盖所有可能的终止位置，并计算以每个可能的起始位置 `i` 开始的连续区段的最大和。
7. 外层循环的结束后，`gs` 将包含整个序列中总和最大的连续区段的和，函数返回这个值作为结果。

### 分治
![[10-Introduction-gsDC.png]]
递推公式得：
$$
T(n)=2T(\frac{n}{2})+O(n)+O(1)
$$
### 减治
考查最短的总和非正后缀 $A[k, hi)$，以及总和最大区段 $GS (lo, hi)=A[i, j)$ ，如下：
![[10-Introduction-gsLS.png]]
- 非正数 (负数和 0)不会是 GS 的前缀；

这段代码使用了一个循环来处理整数序列 `A`，以寻找总和最大的连续区段。下面逐步解释代码的执行过程：

1. 首先，代码定义了三个变量：`gs` 用于存储当前已知的最大和，初始值为序列的第一个元素 `A[0]`；`s` 用于累计当前区段的和，初始值为 0；`i` 用于追踪当前处理的元素位置，初始值为序列的长度 `n`。
2. 使用 `while` 循环，条件是 `0 < i--`，这表示从序列的末尾向开始位置逐步遍历。循环将在当前区间内进行操作，区间的起始位置在 `i` 处。
3. 在循环中，将当前元素 `A[i]` 加到变量 `s` 中，从而递增地累计当前区段的总和。
4. 接着，代码比较 `gs`（当前已知的最大和）与 `s`（当前区段的和）。如果 `s` 更大，则更新 `gs` 为 `s`，从而保持 `gs` 始终存储最大的连续区段和。
5. 然后，代码检查变量 `s` 的值是否小于等于 0。如果是，说明当前区段的和已经为负数或零，这时将变量 `s` 重置为 0。这是减治策略的关键步骤，因为负和的后缀对于整体和的增加没有贡献，所以可以将这部分"剪除"。
6. 继续循环，逐步向序列的起始位置移动，同时更新 `s` 和 `gs` 的值。
7. 循环结束后，变量 `gs` 将包含整个序列中总和最大的连续区段的和，函数返回这个值作为结果。

这种减治策略的思路相对于递增策略具有更好的时间复杂度，为 O(n)，因为它只需一次遍历即可找到最大连续区段和。这是因为在遍历过程中，通过每次比较和重置 `s` 的方式，有效地剪除了负和的后缀，从而保证了在连续区段和变得负数之后能够重新开始寻找可能的更大区段和。

## 动态规划
### 求 fib(n)
递归法求 fib(n) 的复杂度：
![[10-Introduction-fib(n)-complexity.png]]
- 递归深度/空间复杂度 $O(n)$

递归法低效的原因在于，递归实例被重复调用多次，先后出现的递归实例达到 $O(\phi^{n})$ 个，而实际上递归实例的种类不过 $O(n)$ 种。

若能记忆递归实例计算的结果，保存沿途信息，使得后续不必再重复计算，即可降低无用计算。
动态规划——颠倒计算方向，将自顶向下的递归改为自底向上的迭代。

![[10-Introduction-dp-fib.png]]
- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 最长公共子序列
- 子序列：原序列中若干字符，按原相对次序构成，不必连续；

#### 递归
![[10-Introduction-recursion-LCS.png]]

![[10-Introduction-recursion-LCS-impl.png]]

![[10-Introduction-recursion-LCS-analyse.png]]
当两个字符序列 `A` 和 `B` 的内容相似度很低，即它们在大部分位置上没有相同的字符，且长度较长时，递归法求解最长公共子序列问题会陷入最坏情况。最坏情况发生在每次递归调用都需要进行两个分支，即分别调用 `lcs(A, n-1, B, m)` 和 `lcs(A, n, B, m-1)`，这会产生大量的重复实例，导致算法的时间复杂度急剧增加。

假设有两个字符序列： 
A: ABCDEFGHIJKLMNOPQRSTUVWXYZ
B: abcdefghijklmnopqrstuvwxyz

在这个例子中，序列 `A` 包含大写字母，序列 `B` 包含小写字母，它们之间没有相同的字符。这意味着无论怎样的子序列都不会同时出现在两个序列中。因此，在这种情况下，每次递归调用都会产生两个分支，即 `lcs(A, n-1, B, m)` 和 `lcs(A, n, B, m-1)`，并且这两个分支的递归调用也会继续以相同的方式进行，直到序列的长度减少到 0。

考虑其中一个分支，比如 `lcs(A, n-1, B, m)`，这个分支将继续产生类似的分支，一直到 `n` 减少到 0。而另一个分支 `lcs(A, n, B, m-1)` 也会以相同的方式进行。这种情况下，递归调用会产生大量的重复计算，导致时间复杂度急剧上升。

#### 动态规划
```
// 记忆化版
unsigned int lcsMemo(char const* A, int n, char const* B, int m) {
	unsigned int *lcs = new unsigned int[n*m]; //lookup-table of sub-solutions
	memset(lcs, 0xFF, sizeof(unsigned int)*n*m); //initialized with n*m UINT_MAX's
	unsigned int solu = lcsM(A, n, B, m, lcs, m);
	delete[] lcs;
	return solu;
}
unsigned int lcsM( char const * A, int n, char const * B, int m,
unsigned int * const lcs, int const M ) {
	if (n < 1 || m < 1) return 0; //trivial cases
	if (UINT_MAX != lcs[(n-1)*M + m-1]) return lcs[(n-1)*M + m-1]; //recursion stops
	else return lcs[(n-1)*M + m-1] = (A[n-1] == B[m-1]) ? 1 + lcsM(A, n-1, B, m-1, lcs, M) : max( lcsM(A, n-1, B, m, lcs, M), lcsM(A, n, B, m-1, lcs, M) );
}
```

这段代码实现了一个优化过的最长公共子序列（LCS）算法，使用记忆化（也称为动态规划的自顶向下方法）来保存已计算过的子问题的解，从而避免重复计算，提高了算法的效率.

1. `lcsMemo` 函数是主函数，它初始化了一个大小为 `n * m` 的查找表（`lcs`），用于存储子问题的解。这个表的每个元素 `lcs[i][j]` 表示字符串 `A` 的前 `i` 个字符与字符串 `B` 的前 `j` 个字符之间的最长公共子序列长度。
2. `memset` 函数用于将 `lcs` 表的所有元素初始化为无效值 `UINT_MAX`，表示这些子问题的解尚未计算过。
3. `lcsM` 函数是一个递归辅助函数，用于实际计算最长公共子序列的长度，并使用记忆化来避免重复计算。
4. 在 `lcsM` 函数中，首先检查基本情况，即如果 `n` 或 `m` 小于 1，则返回 0，因为这表示一个空串的最长公共子序列长度为 0。
5. 接着，通过查找 `lcs` 表，判断是否已经计算过当前子问题的解。如果已经计算过，就直接返回该解，从而避免了重复计算。
6. 如果**当前子问题的解尚未计算过**，那么进入递归的计算过程。如果当前 `A` 的第 `n-1` 个字符等于 `B` 的第 `m-1` 个字符，则将问题划分为更小的子问题：计算 `A` 的前 `n-1` 个字符与 `B` 的前 `m-1` 个字符之间的最长公共子序列长度，并在其基础上加上 1。
7. 如果当前**字符不相等**，那么问题被划分为两个子问题：计算 `A` 的前 `n-1` 个字符与 `B` 的前 `m` 个字符之间的最长公共子序列长度，以及计算 `A` 的前 `n` 个字符与 `B` 的前 `m-1` 个字符之间的最长公共子序列长度。然后，取这两个子问题解的较大值。
8. 在计算子问题的解后，将其保存到 `lcs` 表中，以便以后的查询，避免了对同一子问题的重复计算。
9. 主函数 `lcsMemo` 在调用 `lcsM` 函数时传入了 `lcs` 表，以便在递归过程中保存已计算过的子问题的解。最终，主函数返回整个序列的最长公共子序列长度。

这种优化方法通过避免重复计算显著提高了算法的效率，将时间复杂度从指数级别（递归法）降低到了线性时间复杂度（$O (n*m)$）。

![[10-Introduction-recursion-LCS-dp.png]]

```
// 从底向上的迭代法DP
unsigned int lcs(char const * A, int n, char const * B, int m) {
	if (n < m) { swap(A, B); swap(n, m); } //make sure m <= n
	unsigned int* lcs1 = new unsigned int[m+1]; //the current two rows are
	unsigned int* lcs2 = new unsigned int[m+1]; //buffered alternatively
	memset(lcs1, 0x00, sizeof(unsigned int) * (m+1));
	memset(lcs2, 0x00, sizeof(unsigned int) * (m+1));
	for (int i = 0; i < n; swap(lcs1, lcs2), i++)
		for (int j = 0; j < m; j++)
			lcs2[j+1] = (A[i] == B[j]) ? 1 + lcs1[j] : max(lcs2[j], lcs1[j+1]);
	unsigned int solu = lcs1[m]; 
	delete[] lcs1; 
	delete[] lcs2; 
	return solu;
}
```

这段代码是使用迭代方法实现的动态规划来计算最长公共子序列（LCS）。相较于之前的递归法，这种迭代方法是自底向上地构建了一个二维数组（或表格）来存储子问题的解，从而避免了递归过程中的重复计算。

1. 首先，代码会检查 `n` 和 `m` 的大小关系，以确保 `m` 小于等于 `n`。这是因为动态规划的思路中，会使用两个数组来保存当前行和前一行的 LCS 值，以交替更新。为了减小空间复杂度，始终选择较短的字符串作为列。
2. 然后，代码创建了两个大小为 `m + 1` 的数组 `lcs1` 和 `lcs2`，用于存储当前行和前一行的LCS值。
3. 通过 `memset` 函数，将 `lcs1` 和 `lcs2` 数组初始化为全零。
4. 使用两个嵌套的循环，外层循环遍历字符串 `A`，内层循环遍历字符串 `B`。
5. 在循环中，根据动态规划的思想，计算 `lcs2[j + 1]`，即当前位置 `(i, j)` 处的LCS值。这是根据以下两种情况来计算的：
    - 如果 `A[i]` 等于 `B[j]`，则说明这两个字符可以贡献到LCS，所以 `lcs2[j + 1]` 将等于 `1 + lcs1[j]`，其中 `lcs1[j]` 表示上一行前一列的LCS值。
    - 如果 `A[i]` 不等于 `B[j]`，则需要在 `lcs2[j]`（当前行前一列的LCS值）和 `lcs1[j + 1]`（上一行当前列的LCS值）之间选择较大的值，因为当前字符不匹配，LCS值保持不变。
6. 每次内层循环结束时，`lcs2` 数组将更新为当前行的LCS值。
7. 外层循环结束后，整个 `lcs1` 数组中的最后一个元素即为最终结果，它表示字符串 `A` 和字符串 `B` 的最长公共子序列长度。
8. 最后，释放之前分配的内存，即 `lcs1` 和 `lcs2` 数组。

## 番外

## 习题集结论
### 1-12 countOnes 算法改进
```
int countOnes(unsigned int n){
	// 统计整数二进制展开中数位1的总数
} //O(logn)
```

### 1-14 power 2 ()迭代版
```
// power2() 递归版
inline __int64 sqr(__int64){return a*a;}
__int64 power2(int n){
	//计算幂函数2^n
	if(0==n) return 1;//递归基，否则视n的奇偶分别递归
	return (n&1)?sqr(power2(n>>1))<<1 : sqr(power2(n>>1));
} // O(logn)=O(r),r为输入指数n的比特位数
```


### 1-15 找出整数序列最大者
#### 迭代版
```
int maxI ( int A[], int n ) {
	int m = INT_MIN; //初始化最大值记录，O(1)
	for ( int i = 0; i < n; i++ ) //对全部共O(n)个元素，逐一
		m = max ( m, A[i] ); //比较并更新，O(1)
	return m; //迒回最大值，O(1)
} 
```

$O (1) + O (n)*O (1) + O (1) = O (n+2) = O (n)$ 

#### 线性递归版
```
int maxR ( int A[], int n ) {
	if ( 2 > n ) //平凡情况，递归基
		return A[n - 1]; //直接（非递归式）计算
	else //一般情况，递归：在前n - 1项中最大值与第n - 1项之间，取大者
		return max ( maxR ( A, n - 1 ), A[n - 1] );
}
```

$O (1) * 递归深度 = O (1) * (n + 1) = O (n)$ 

#### 二分递归版
```
 int maxR ( int A[], int lo, int hi ) { 
	if ( lo + 1 == hi ) //如遇递归基（区间长度已降至1），则
		return A[lo]; //直接返回该元素
	else { //否则（一般情况下lo + 1 < hi），则递归地
		int mi = ( lo + hi ) >> 1; //以中位单元为界，将原区间一分为二：A[lo, mi)和A[mi, hi)
		return max ( maxR ( A, lo, mi ), maxR ( A, mi, hi ) ); //计算子区间的最大值，再从中取大者
	}
}
```

$O (hi - lo)$，线性正比于区间长度

### 1-17 证明空间复杂度与递归深度关系
**试证明**：若每个递归实例仅需使用常数规模的空间，则递归算法所需空间总量将线性正比于最大递归深度。 

**解    答**：
根据递归跟踪分析法，在递归程序的执行过程中，系统必须动态地记录所有活跃的递归实例。在任何时刻，这些活跃的递归实例都可按照调用关系，构成一个调用链，该程序执行期间所需的空间，主要用于维护上述调用链。不难看出，按照题目所给的条件，这部分空间量应线性正比于 调用链的最大长度，亦即最大的递归深度。 

在教材的4.2.1节，还将针对递归实现机制——*函数调用栈*——做详细的介绍。届时，将了解上述过程更多的具体细节。简而言之，以上所定义的调用链，实际上就对应于该栈中的所有帧。在任何时刻，其中每一对相邻的帧，都对应于存在“调用与被调用”关系的一对递归实例。若各递归实例所需空间均为常数量，则空间占用量与栈内所含帧数成正比，并在递归达到最深层时达到最大。

### 1-19 Fib 二分递归的细节
#### fib (k)的递归实例在执行中出现了多少次？
在该算法的递归跟踪图中，每向下递归深入一层，入口参数就减一（向左）或减二（向右）。在从入口 fib(n)通往每一 fib(k)递归实例的沿途，各递归实例的入口参数只能依次减一或减二。因此，fib(k)出现的次数，应该等于从 n 开始，经每次减一或减二，最终减至 k 的路径总数。
![[10-Introduction-fib(k).png]]
考查这些路径的最后一步，如图 x1.3所示无非两种可能：或由 fib(k + 1)向左抵达 fib(k)，或由 fib(k + 2)向右抵达 fib(k)。因此，若将 fib(k)出现的次数记作 F(k)，则可得递推式如下： 
- $F (n) = 1$
- $F (k) = F (k + 1) + F (k + 2)$
两式联合求解，即得：
- $F(k) = fib(n - k + 1)$

#### 证明即使是打印 fib (n)也需要Ω(n)时间
从渐进角度看，$fib(n) = Θ(Φ^{n} )$。因此采用任何常数进制展开，fib(n)均由 $Θ(n)$ 个数位组成。这就意味着，即便已知 fib(n)的数值大小，将该数值逐位打印出来也至少需要 $Θ(n)$ 时间。

#### 尝试 O (logn)时间内计算出 fib(n)
根据 Fibonacci 数的定义，可得如下矩阵形式的递推关系：
$$
\begin{pmatrix}
0\ 1\\1\ 1 
\end{pmatrix}
\begin{pmatrix}
fib(k-1)\\fib(k)
\end{pmatrix}
=
\begin{pmatrix}
fib(k)\\fib(k+1)
\end{pmatrix}
$$
递推可得：
$$
\begin{pmatrix}
fib(n)\\fib(n+1)
\end{pmatrix}
=
\begin{pmatrix}
0\ 1\\1\ 1 
\end{pmatrix}^{n}
\begin{pmatrix}
fib(0)\\fib(1)
\end{pmatrix}
=
\begin{pmatrix}
0\ 1\\1\ 1 
\end{pmatrix}^{n}
\begin{pmatrix}
0\\1 
\end{pmatrix}
$$

于是，若套用 power2()算法的流程，只要将其中的整数平方运算 sqr()换成矩阵的平方运算，即可实现 fib(n)的计算。更重要的是，这里仅涉及2x2矩阵的计算，每次同样只需常数时间，故整体的运行时间也是 O(logn)。

```
// power2()
inline __int64 sqr(__int64){return a*a;}
__int64 power2(int n){
	//计算幂函数2^n
	if(0==n) return 1;//递归基，否则视n的奇偶分别递归
	return (n&1)?sqr(power2(n>>1))<<1 : sqr(power2(n>>1));
} // O(logn)=O(r),r为输入指数n的比特位数
```

>[!note] 这两个问题是否构成悖论？
>以上结论在表面上的确构成悖论。究其根源在于，以上对 power 2 ()与 fib ()等算法的时间复杂度分析都假定，整数的乘法、位移和打印等基本操作各自只需 O (1)时间——即采用所谓的常数代价准则（uniform cost criterion）——而这只是在一定程度上的近似。
>
>设参与运算的整数（的数值）为 k。不难看出，上述基本操作都需要逐个地读取 k 的二进制展开的每一有效比特位，故更为精确地，这些操作的时间成本应该线性正比于 k 的有效位的总数 O (logk)——即采用所谓的对数代价准则（logarithmic cost criterion）。 
>
>当 k 不是很大时，两种准则之间的差异并不是不大；而当 k 很大甚至远远超出机器字长之后，二者之间的差异将不容忽略。仍以 Fibonacci 数为例。因 $fib(n) = Θ(Φ^n)$，故该数列应以 $Φ$ 为比率呈指数递增，各项的二进制展开长度 $log_2(Φ^n)$ 则以匀速呈线性递增。根据之前所给的估算经验，相邻项约相差 $log_2Φ = 0.694$ 个比特，大致每隔 36 项相差 25 个比特。也就是说，自 fib (48)后便会导致 32 位无符号整数的溢出，自 fib (94)后便会导致 64 位无符号整数的溢出。

### 1-25 LCD
#### 更相减损术
过程描述：
1. 令 p = 1
2. 若 a 和 b 不都是偶数，则转 5)
3. 令 p = p * 2，a = a/2，b = b/2
4. 转 2)
5. 令 t = |a - b|
6. 若 t = 0，则返回并输出 a * p
7. 若 t 为奇数，则转10)
8. 令 t = t/2
9. 转 7)
10. 若 a >= b，则令 a = t；否则，令 b = t
11. 转5)

```
__int64 gcdCN ( __int64 a, __int64 b ) { //assert: 0 < min(a, b)
2 int r = 0; //a和b癿2^r形式癿公因子
3 while ( ! ( ( a & 1 ) || ( b & 1 ) ) ) { //若a和b都是偶数
4 a >>= 1; b >>= 1; r ++; //则同时除2（右秱），幵累加至r
5 } //以下，a和b至夗其一为偶
while ( 1 ) {
7 while ( ! ( a & 1 ) ) a >>= 1; //若a偶（b奇），则剔除a癿所有因子2
8 while ( ! ( b & 1 ) ) b >>= 1; //若b偶（a奇），则剔除b癿所有因子2
9 ( a > b ) ? a = a - b : b = b - a; //简化为：gcd(max(a, b) - min(a, b), min(a, b))
10 if ( 0 == a ) return b << r; //简化至平凡情冴：gcd(0, b) = b
11 if ( 0 == b ) return a << r; //简化至平凡情冴：gcd(a, 0) = a
12 }
13 }
```

#### 与辗转相除法比较
首先可以证明，算法“中”的渐进时间复杂度依然是 O(log(a + b))。
考查该算法的每一步迭代，紧接于两个内部while循环之后设置一个断点，观察此时的a和b。
实际上，在a和b各自剔除了所有因子2之后，此时它们都将是奇数。接下来，无论二者大小如何，
再经一次互减运算，它们必然将成为一奇一偶。比如，不失一般性地设a > b，则得到：
a - b （偶）
b （奇）
再经一步迭代并重新回到断点时，前者至多是：
(a - b)/2
两个变量之和至多是：
(a - b)/2 + b  (a + b)/2
可见，每经过一步迭代，a + b至少减少一半，故总体迭代步数不超过：
log2(a + b)
另外，尽管从计算流程来看，算法“中”的步骤似乎比算法“欧”更多，但前者仅涉及加减、
位测试和移位（除2）运算，而不必做更复杂的乘除运算。因此，前者更适于在现代计算机上编
程实现，而且实际的计算效率更高。
反之，无论是图灵机模型还是RAM模型②，除法运算在底层都是通过减法实现的。因此，对
于算法“欧”所谓的“除法加速”效果，不可过于乐观而在输入整数大小悬殊时，尤其如此。
最后，较之算法“欧”，算法“中”更易于推广至多个整数的情况。

### 1-26 实现就地循环左移
#### 迭代版

#### reverse 版
```
int shift2 ( int* A, int n, int k ) { //倚劣倒置算法，将数组循环左秱k位，O(3n)
k %= n; //确保k <= n
reverse ( A, k ); //将匙间A[0, k)倒置：O(3k/2)次操作
reverse ( A + k, n - k ); //将匙间A[k, n)倒置：O(3(n - k)/2)次操作
reverse ( A, n ); //倒置整个数组A[0, n)：O(3n/2)次操作
return 3 * n; //迒回累计操作次数，以便不其它算法比较：3/2 * (k + (n - k) + n) = 3n
}

```
![[10-Introduction-reverse-csl.png]]
该算法的其它版本有可能只需更少的交换操作，故单就此指标而言，似乎更加“优于”以上版本。然而就实际的计算效率而言，以上版本却要远远优于其它版本。 

究其原因在于，reverse()之类的操作所涉及的数据元素，在物理上是连续分布的，因此操作系统的缓存机制可以轻易地被激活，并充分发挥作用；其它版本的交换操作尽管可能更少，但数据元素在空间往往相距很远，甚至随机分布，缓存机制将几乎甚至完全失效。

在实际的算法设计与编程中，这些方面也是首先必须考虑的因素；在当下，面对规模日益膨胀的大数据，这方面的技巧对算法的实际性能更是举足轻重。

### 1-27 Ackermann 函数必然终止否？
$$
A(m, n) = \begin{cases}
              n + 1 & \text{if } m = 0 \\
              A(m - 1, 1) & \text{if } m > 0 \text{ and } n = 0 \\
              A(m - 1, A(m, n - 1)) & \text{if } m > 0 \text{ and } n > 0
          \end{cases}

$$
在可计算性理论中，Ackermann 函数是典型的非原始递归的递归函数。尽管其定义和计算过
程较为复杂，依然可以证明其计算过程必然终止，故对任何(m, n)参数组合均有明确的定义。
以下，可以采用超限数学归纳法（transfinite induction）来证明上述论断。
为此，我们首先需要在所有非负整数的组合(m, n)之间，定义如下次序：
对于任何(m1, n1)与(m2, n2)，若m1 < m2，或者m1 = m2且n1 < n2，则称前者
小于后者，记作(m1, n1) < (m2, n2)
实际上，所有的(m, n)组合与平面上第一象限内的整点一一对应。不难看出，任何两个整
点都可按照这一定义比较大小，故这是一个全序。更重要地，该整点集的任何一个子集，都有最
小元素即该子集中的最左最低点（leftmost-then-lowest point）。其中特别地，全集
的最小元素即为坐标原点(0, 0)。因此，如上定义的次序“<”，的确是一个良序（well order）。
由定义，任意形如(0, n)的输入都会立即终止这可作为归纳基础。
作为归纳假设，不妨假定：对于任意小于(m, n)的输入，Ackermann函数均能终止。现考
查输入参数为(m, n)时，该函数的可终止性。
依然由定义可见，此时可能引发的递归实例无非三类：
Ackermann(m - 1, 1)
Ackermann(m - 1, \*)
Ackermann(m, n - 1)
可见，根据如上约定的次序，其对应的参数组合均小于(m, n)。故由归纳假设，以此参数组合
对该函数的调用，亦必然会终止。

### 1-28 咖啡罐问题

### 1-29 Hailstone 的迭代实现

### 1-30 递归、级数求和与迭代、递推方程

### 1-32 分析以下函数的渐进复杂度
