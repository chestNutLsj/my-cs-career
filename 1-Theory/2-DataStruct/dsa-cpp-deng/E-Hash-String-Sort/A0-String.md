## 串的相关术语
![[A0-String-instance.png]]
- 子串：$S.substr(i,k)=S[i,i+k),0\le i<n,0\le k$ 
	- ![[A0-String-substr.png]]
- 前缀：$S.prefix(k)=S.substr(0,k)=S[0,k),0\le k\le n$ 
	- ![[A0-String-prefix.png]]
- 后缀：$S.suffix(k)=S.substr(n-k,k)=S[n-k,n),0\le k\le n$ 
	- ![[A0-String-suffix.png]]

三者关系：$S.substr(i,k)=S.prefix(i+k).suffix(k)=S.suffix(n-i).prefix(k)$ 
## ADT
![[A0-String-ADT.png]]

本章的重点在串的模式匹配算法。

## 模式匹配问题
模式匹配：从一段文本（称为 text）中，查找是否存在想要的内容（称为模式 pattern），若存在则返回位置或其它信息。（暗含着，模式串是与文本串相关联甚至就是其中子串的可能）

其中，文本串的长度 `|T|=n`，模式串的长度 `|P|=m`，他们之间存在这样的关系：2 << m << n。
并且，它们的字符集Σ，其中字符种类有 `s = |Σ|` 个，因此匹配的概率：$Pr_{match}\approx \frac{n}{s^{m}}\rightarrow 0$ 

## 蛮力策略
### 思路与复杂度
![[A0-String-BF.png]]
- 逐位查看是否匹配，若不匹配，则后移一位继续查找；
- **最好情况**：不匹配的情况仅发生在第一位，因此模式串能够快速滑动越果失配之处——到达匹配处或文本串末尾——时间复杂度Ω(n)
- **最坏情况**：每一次查看都是匹配，但直到模式串 P 的末尾才失配——每轮查看都反复地逐位比较模式串的每一位——时间复杂度 `O(n*m)` 
	- 比如：模式串 P 为 0001，而文本串为 00000000...

### 实现：版本 1
```
/************************************************
 * Text :  0 1 2 . . . i-j . . . i . . . . . n-1
 *         ------------|---------|------------
 * Pattern:            0 . . . . j . .
 *                     |---------|
 ************************************************/

int match ( char* P, char* T ) { //串匹配算法（Brute-force-1）
	size_t n = strlen ( T ), i = 0; //文本串长度、当前接受比对字符的位置
	size_t m = strlen ( P ), j = 0; //模式串长度、当前接受比对字符的位置
	while ( j < m && i < n ) //自左向右逐个比对字符
	    if ( T[i] == P[j] ) //若匹配
	        { i ++;  j ++; } //则转到下一对字符
	    else //否则
	        { i -= j - 1; j = 0; }//T串回退、P复位
	return i - j; //最终的对齐位置：藉此足以判断匹配结果
}
```

### 实现：版本 2
```
/************************************************
 * Text :  0 1 2 . . . i i+1 . . . i+j . . n-1
 *         ------------|-----------|------------
 * Pattern  :          0 1  .  .   j . .
 *                     |-----------|
 *************************************************/
int match ( char* P, char* T ) { //串匹配算法（Brute-force-2）
	size_t n = strlen ( T ), i = 0; //文本串长度、与模式串首字符的对齐位置
	size_t m = strlen ( P ), j; //模式串长度、当前接受比对字符的位置
	for ( i = 0; i < n - m + 1; i++ ) { //文本串从第i个字符起，与
	    for ( j = 0; j < m; j++ ) //模式串中对应的字符逐个比对
	        if ( T[i + j] != P[j] ) break; //若失配，模式串整体右移一个字符，再做一轮比对
        }
	    if ( j >= m ) break; //找到匹配子串
	}
	return i; //最终的对齐位置：藉此足以判断匹配结果
}
```

两个版本是等价的。

## KMP 法
### 思路
![[A0-String-kmp-idea1.png]]
- 在任一时刻，都有 `T[ i-j, i ) == P[ 0, j )` 
- 亦即，我们业已掌握 `T[i-j,i)` 的所有信息，既如此，一旦失配，就应已知哪些位置值得/不必对齐，而且在下一轮比对中 `T[i−j',i)` 可径直接受，而不必再做比对：
![[A0-String-kmp-idea2.png]]
如此，i 将永远不必回退！(BF 算法的低效之处，正在于此 —— 一旦失配就要回退) 
- 比对成功，则与 j 同步前进一个字符
- 否则，j 更新为某更小的 t，并继续比对
	- 如此优化，可以使得 P 快速右移，并且避免重复比对已确定的子串
	- 那么，为确定移动的 t，需花费多少时间和空间？更重要地，可否在事先就确定？——构造 `next[]` 表。

### `next[]` 查询表

要移动的距离 t，不仅可以事先确定，而且仅根据 `P[0,j) = T[i-j,i)` 即可确定：
![[A0-String-kmp-next.png]]
- 视失败的位置 j，无非 m 种情况 (即是模式串的长度)
- 构造查询表 `next[0,m)`，做好预案
- 一旦在 `P[j]` 处失配，只需将 j 替换为 `next[j]`，继续与 `T[i]` 比对即可。
- 换言之，`next[j]` 就是模式串 `P[0, j-1)` 子串的公共真前缀、真后缀的最大匹配长度。

### `next[]` 表实例
![[A0-String-kmp-next-instance.png]]
![[A0-String-kmp-instance-2.png]]
>[! example] 另举一例：
>构造 next 表：
> `[index]` ： 0  1  2  3  4  5  6  7  8  9 10  11
> `Pattern` ： A  B  C  A  A  B  B  A  B  C  A   B 
> `next[j]` ：-1  0  0  0  1  1  2  0  1  2  3   4

### KMP 算法的实现
```
int match ( char* P, char* T ) {  //KMP算法
	int* next = buildNext ( P ); //构造next表
	int n = ( int ) strlen ( T ), i = 0; //文本串指针
	int m = ( int ) strlen ( P ), j = 0; //模式串指针
	while ( j < m  && i < n ) { //自左向右逐个比对字符  
	    if ( 0 > j || T[i] == P[j] ) //若匹配，或P已移出最左侧（两个判断的次序不可交换）
	        { i ++;  j ++; } //则转到下一字符
	    else //否则
	        j = next[j]; //模式串右移（注意：文本串不用回退）

	delete [] next; //释放next表
	return i - j;
}

int* buildNext ( char* P ) { //构造模式串P的next表
	size_t m = strlen ( P ), j = 0; //“主”串指针
	int* next = new int[m]; int t = next[0] = -1; //next表，首项必为-1
	while ( j < m - 1 )
	    if ( 0 > t || P[t] == P[j] ) { //匹配
	        ++t; ++j; next[j] = t; //则递增赋值：此处可改进...
	    } else //否则
	        t = next[t]; //继续尝试下一值得尝试的位置
	return next;
}
```

### 理解 `next[]` 表
![[A0-String-kmp-next-comprehense.png]]

- 所有自匹配的长度： $\forall j\ge 1,N(P,j) = \{0le t<j|P[0,t)=P[j-t,j)\}$
- 因总包含 0 而非空：$0\in N(P,j)\ne \varnothing$ 
- 故可以取最大长度：位移最小，不致回溯：$next[j]=max\{N(P,j)\}$
- $next[0]\equiv 0$ 

![[A0-String-kmp-next-comprehense-2.png]]
- 记：$next^{0}[j]=j,next^{k+1}[j]=next[next^{k}[j]],(k\ge 0\ and\ next^{k}[j]\ge 0)$ 
- 同一文本串中元素 `T[i]` 可能依次与模式串 P 中的多个字符比对，其秩是：$j=next^{0}[j],next^{1}[j],next^{3}[j],...,0,-1$ 

### 构造 `next[]` 表
![[A0-String-kmp-next-build.png]]
模式串 P 在 j+1 处的自匹配，只比在 j 处的自匹配多出一个字符，即：$next[j+1]=next[j]+1\quad iff. \quad P[j]=P[next[j]]$：
![[A0-String-kmp-next-build-2.png]]
```
int* buildNext ( char* P ) { //构造模式串P的next表
	size_t m = strlen ( P ), j = 0; //“主”串指针
	int* next = new int[m]; int t = next[0] = -1; //next表，首项必为-1
	while ( j < m - 1 )
	    if ( 0 > t || P[t] == P[j] ) { //匹配
	        ++t; ++j; next[j] = t; //则递增赋值：此处可改进...
	    } else //否则
	        t = next[t]; //继续尝试下一值得尝试的位置
	return next;
}
```

![[A0-String-kmp-next-build-3.png]]

![[A0-String-kmp-build-next-2013-1.png]]
![[A0-String-kmp-build-next-2013-2.png]]
![[A0-String-kmp-build-next-2013-3.png]]
![[A0-String-kmp-build-next-2013-4.png]]
### 分摊分析
![[A0-String-kmp-analyze.png]]
- 每个 `T[i]`（红），都可能参与 Ω(m)次比对（黄）
- 倘若共有 Ω(n) 个这样的 `T[i]`... 难道要达到 `Ω(n*m)` 吗？
- 然而更细致的分析将表明 即便在最坏情况下，累计也不过 $2n=O (n)$ 次
- 同理，构造 `next[]` 也只需 O (m)

```
令：`k = 2*i - j` //欠精准，但还算够用的计步器
while ( j < m && i < n ) //k必随迭代而单调递增，故也是迭代步数的上界 
	if ( 0 > j || T[i] == P[j] ) 
		{ i ++; j ++; } //k 恰好加1 
	else j = next[j]; //k 至少加1 
```

初始：k = 0
算法结束时：`k = 2*i - j <= 2(n - 1) - (-1) = 2n - 1` 

因此总体复杂度：构造 `next[]` O (m) + 查询 O (n) = O (m+n)

### 改进 `next[]`
![[A0-String-kmp-improved-next-1.png]]
- 上图是一个反例：
	- 先在 `T[3]` 处比较失败
	- 又：与 `P[3]` 比对，失败 
	- 双：与 `P[2] = P[next[3]]` 比对，失败 
	- 叒：与 `P[1] = P[next[2]]` 比对，失败 
	- 叕：与 `P[0] = P[next[1]]` 比对，失败
- 最终，才前进到 `T[4]` 
- 事实上，后三次比较本来就可以避免，没有必要一错再错：

```
int* buildNext ( char* P ) { //构造模式串P的next表（改进版本）
	size_t m = strlen ( P ), j = 0; //“主”串指针
	int* next = new int[m]; int t = next[0] = -1; //next表，首项必为-1
	while ( j < m - 1 )
	    if ( 0 > t || P[t] == P[j] ) { //匹配
	        if ( P[++t] != P[++j] ) //附加条件判断
		        next[j] = t; //唯当新的一对字符也匹配时，方照原方法赋值
	        else
		        next[j] = next[t]; //否则，改用next[t]（此时必有：P[next[t]] != P[t] == P[j]）
	    } else //失配
	        t = next[t]; //继续尝试下一值得尝试的位置
   return next;
}
```

![[A0-String-kmp-next-improved-compare.png]]

![[A0-String-kmp-improved-next-instance.png]]

### 适用范围
- 特别适用于顺序存储介质
- 单次匹配概率越大（字符集越小），优势越明显 //比如二进制串
- 否则，与蛮力算法的性能相差无几...