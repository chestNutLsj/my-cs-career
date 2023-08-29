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
- 等比数列求和

收敛级数：$O(1)$
- $\sum\limits_{k=2}^{n}\frac{1}{(k-1)\cdot k}=\frac{1}{1\cdot 2}+\frac{1}{2\cdot 3}+...+\frac{1}{(n-1)\cdot n}=1-\frac{1}{n}=O(1)$
- $\sum\limits_{k\ is\ a\ perfect\ power}\frac{1}{k-1}=\frac{1}{3}+\frac{1}{7}+\frac{1}{8}+\frac{1}{15}+...=1=O(1)$

几何分布: $O(1)$
- $(1-\lambda)\cdot[1+2\lambda+3\lambda^{2}+4\lambda^{3}+...]=\frac{1}{1-\lambda}=O(1),0<\lambda<1$

调和级数：$\Theta(logn)$
- $h(n)=\sum\limits_{k=1}^{n}\frac{1}{k}=1+\frac{1}{2}+\frac{1}{3}+...=lnn+\gamma+O(\frac{1}{2n})=\Theta(logn)$

对数级数：$\Theta(nlogn)$
- $\sum\limits_{k=1}^{n}lnk=ln\prod\limits_{k=1}^{n}=ln(n!)\approx (n+0.5)lnn-n=\Theta(nlogn)$
- stirling formulation: $n!\approx \sqrt{2\pi n}\cdot (\frac{n}{e})^n$ 

### 迭代

## 迭代与递归
### 减治

### 分治