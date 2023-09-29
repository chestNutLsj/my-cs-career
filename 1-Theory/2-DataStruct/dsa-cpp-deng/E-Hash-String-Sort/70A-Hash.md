## Dictionary ADT
```
template <typename K, typename V> //key、value
struct Dictionary {
	virtual Rank size() = 0;
	virtual bool put( K, V ) = 0;
	virtual V* get( K ) = 0;
	virtual bool remove( K ) = 0;
};

```

词典中的词条只需支持判等/比对操作。

词条 entry=(key, value)，词条的集合作为 Map/Dictionary，并且关键码 key 有雷同性限制。

关键码 key 可以是任何类型，因此未必支持大小比较。

## 散列
数组存放数据的查询效率很高，可以达到 O(1)。但是所有空间常常不能占满，例如以电话号码为例：
- 可能的电话号种数=R=10^11
- 实际可用的电话号种数=N=25000
- 因此实际的空间复杂度= O (R+N)，效率=25K/10^5M=0.000025%

为了提高空间利用率，又保证查找速度，可以使用散列法：
![[70-Hash-hashing.png]]
- 桶 bucket：直接存放或间接指向的**一个词条**；
- Bucket array ~ Hashtable：容量 M (N<M<<R)，使得空间复杂度 O (N+M)=O (2N+C)=O (N)
- 定址：根据词条的 key，直接（实际是通过散列函数和冲突解决策略）确定散列表的入口
- 散列函数：hash (): key --> &entry
- “直接”：expected-O (1) ，而不是 O (1)

## 冲突
指不同的 key，在经过 hash 函数运算后得到相同的结果：
![[70-Hash-synonym.png]]

考虑装填因子 load factor: λ=N/M
- λ越大，空间利用率越高，但相应的发生冲突的概率越高。
- 通过降低 λ，即增加 Hashtable 的容量 M，可以改善冲突程度，但只要 M<<R 这一条件存在，冲突就不可能杜绝。

### 完美散列
数据集已知且固定时，可实现完美散列（perfect hashing） 
- 采用两级散列模式
- 仅需 O(n)空间 
- 关键码之间互不冲突
- 最坏情况下的查找时间也不过 O (1) 
- 不过在一般情况下，完美散列可期不可求...
![[70-Hash-perfect-hash.png]]

### 生日悖论
将在座同学（对应的词条）按生日（月/日）做散列存储，散列表长 M = 365，装填因子 = 在场人数 N / 365 

冲突（至少有两位同学生日相同）的可能性 P365(n) = ？ 
P365(1) = 0, P365(2) = 1/365, ..., P365(22) = 47.6%, P365(23) = 50.7%, ... 

100人的集会：1 - P365(100) = 0.000,031% 

因此，在装填因子确定之后，散列策略的选取将至关重要，同时，发生冲突时尽快、高效地排解也非常重要。

## 散列函数
### 评价标准
1. 确定 determinism：统一关键码总是映射到同一地址
2. 快速 efficiency： 映射的计算时间开销为 expected-O (1)
3. 满射 surjection： 尽可能充分地利用整个散列空间 Hashtable
4. 均匀 uniformity： 关键码散射到散列表各位置的概率尽量接近，有效避免聚集 clustering 现象
	- 装填因子很大时，也难以避免聚集现象的发生

### 除余法
**hash(key) = key % M**

#### 素数的作用
据说：M 为素数时，数据对散列表的覆盖最充分，分布最均匀
![[prime&bucket.svg]]
若取 M=2^k，效果相当于截取 key 的最后 k 位，前面的 n-k 位对地址都没有影响：
- M-1 = 0000... 0|11... 11
- key % M = key & (M-1)
- 推论：发生冲突时，当且仅当最后 k 位相同，此时发生冲突的概率极大
- 因而，取 M != 2^k，缺陷会有所改善
- 从而，M 为素数时，数据对散列表的覆盖最充分、分布最均匀

蝉的哲学：经长期自然选择，生命周期“取”作素数
- 蝉通常是十三龄蝉、十七龄蝉这样的素数年龄；
- 这里十三龄、十七龄就是除余法中的 M，而 step 是天敌的生命周期；

#### M 不是素数可以吗？
其实：对于理想随机的序列，表长是否素数，无关紧要！ 

序列的 Kolmogorov 复杂度：生成该序列的算法，最短可用多少行代码实现？ 
- 算术级数： 7 12 17 22 27 32 37 42 47 ... //单调性/线性：从 7 开始，步长为5
- 循环级数： 1 2 3 4 5 1 2 3 4 5 1 2 3 4 5 ... //周期性：12345
- 英文：data structures and algorithms ... //局部性：频率、关联、词根、... 

实际应用中的数据序列远非理想随机，上述规律性普遍存在。

#### 缺陷
- 不动点：无论 M 取值如何，总有 hash (0) = 0 
>违背任何元素映射到任意点的概率相同的原则

- 相关性：\[0, R)的关键码尽管平均分配至 M 个桶，但相邻关键码的散列地址也必然相邻:

### MAD 法
multiply-add-divide: 取 M 为素数，a>1, b>0, a%M != 0
**hash (key) = (a * key + b) % M**

![[70-Hash-MAD.png]]
- 这里 a 是步长，消除零阶均匀的问题
- b 是消除不动点的问题

### 数字分析法
selecting digits：抽取 key 中的某几位，构成地址
- 比如，取十进制表示的奇数位：hash (3141592654)=34525
- 不过，由于偶数位对 hash 的结果没有贡献，因此影响了函数的均匀性；

### 平方取中法
取 key^2的中间若干位，构成地址:
- hash (123)=middle (123 * 123)=middle (1 512 9) = 512
- hash (1234567)=middle (1524155677489)=556

![[70-Hash-mid-square.png]]
中间位置的密度最大，由各个位置的数都参与贡献得到。

### 折叠法
folding：将 key 分割成等宽的若干段，取其总和作为地址
- hash (123456789)=123+456+789=1368
- hash (123456789)=123+654+789=1566

### 位异或法
XOR：将 key 分割成等宽的二进制段，经异或运算得到地址
- hash (110011011)=110^011^011=110
- hash (110011011)=110^110^011=011

### 伪随机数法
- 伪随机数发生器：rand (x+1)=\[a * rand (x)]%M, 
	- M is prime && a%M !=0
	- 伪随机数发生器因平台、历史版本而有差异，故程序的可移植性差；

- 伪随机数法：hash (key)=rand (key)=\[rand (0) * a^key]%M

- 种子 rand (0)

```
// The C Programming Language 第二版p46页的伪随机数发生器

unsigned long int next = 1; //sizeof(long int) = 8
void srand(unsigned int seed) { next = seed; } //sizeof(int) = 4 or 8

int rand(void) {//1103515245 = 3^5 * 5 * 7 * 129749
	next = next * 1103515245 + 12345;
	return (unsigned int)(next/65536) % 32768;
}
```

![[70-Hash-pseudorandom.png]]

```
// 其它伪随机数生成方法

// 利用内存分配的随机性
int rand() {
	int uninitialized;
	return uninitialized;
}

char* rand( t_size n ) {
	return ( char* ) malloc( n );
}

// 就地随地置乱(Fisher方法)
void shuffle( int A[], int n ) {
	for ( ; 1 < n; --n ) //自后向前，依次将各元素
	swap( A[ rand() % n ], A[ n - 1 ] ); //与随机选取的某一前驱（含自身）交换
} //20! < 2^64 < 21!

```

![[70-Hash-fisher-shuffle.png]]
<center>Fisher shuffle</center>

Fisher 方法可以等概率生成所有 n! 种排列吗？

### Hashcode 与多项式法
```
static Rank hashCode( char s[] ) {
	Rank n = strlen(s); Rank h = 0;
	for ( Rank i = 0; i < n; i++ ) {
		h = (h << 5) | (h >> 27);
		h += s[i];
	} //乘以32，加上扰动，累计贡献
	return h;
}
```

$$
\begin{aligned}
&hashCode(x_{n-1}x_{n-2}...x_{2}x_{1}x_{0})\\
&=x_{n-1}\cdot a^{n-1}+...+x_{2}\cdot a^{2}+x_{1}\cdot a^{1}+x_{0}\\
&=(...((x_{n-1}\cdot a+x_{n-2})\cdot a)+...+x_{1)\cdot}a+x_{0}
\end{aligned}
$$

![[70-Hash-hashcode.png]]

还可以将字符映射为数字，再简单相加：$hash(string)=\sum\limits_{char\in S}code(upper(char))$
- 这样字符串中的相对次序的信息丢失，会引发大量冲突
- 甚至不同数量的字符也有可能获得同样的结果：
	- He is Harry Potter   196
	- I am Lord Voldemort  196
	- Tom Marvolo Riddle   196

## 排解冲突
### 开放散列
#### 多槽位
- 将桶单元细分成若干槽位，以存放同一单元冲突的词条
- 但是问题在于，槽位究竟要分配多少？这难以确定

![[70-Hash-multiple-slots.png]]

#### 公共溢出区
- 单独开辟一块连续空间，发生冲突的词条，按顺序存入此区域；
- 结构简单，算法易于实现，
- 但是不冲突则以，一旦冲突，最坏情况下处理冲突词条所需的时间将正比于溢出区的规模：

![[70-Hash-overflow-area.png]]

#### 独立链
- 每个桶对应一个链表，存放发生冲突的一组同义词
- **优点**：无需为每个桶预备多个槽位，任意多次冲突都可以解决，删除操作实现简单、统一
- **缺点**：指针本身占用空间，节点的动态分配和回收需要消耗时间，更重要的是==空间不连续分布，系统缓存很难生效==
![[70-Hash-linkedlist-chaining.png]]

### 封闭散列
#### 开放定址
Closed Hashing，必然要有对应的 Open Addressing 
- 只要有必要，任何散列桶都可以接纳任何词条

Probe Sequence/Chain 
- 为每个词条，都需事先约定若干备用桶，优先级逐次下降
- 查找算法：沿试探链，逐个转向下一桶单元，直到
	- 命中成功，或者 
	- 抵达一个空桶（存在则必能找到？）而失败

相应地，试探链又应如何约定？

#### 线性试探
Linear Probing：
- 一旦冲突，则试探后一紧邻的桶，直到命中或抵达空桶
- 在散列表**内部解决冲突**，无需附加的指针、链表或溢出区等，整体结构保持简洁。只要还有空桶，迟早会找到
- 新增非同义词之间的冲突——数据堆积（clustering）现象严重
- 好在，试探链连续，数据局部性良好
- 通过装填因子，冲突与堆积都可有效控制
- ![[70-Hash-linear-prob.png]]
![[70-Hash-linear-probing.png]]
- 插入：新词条若尚不存在，则存入试探终止处的空桶
- 试探链可能因而彼此串接、重叠！
- 删除：不能直接清除命中的桶——经过它的试探链都将因此断裂，导致后续词条丢失——明明存在，却访问不到
	- **懒删除**：仅做标记，不对试探链做更多调整——此后，带标记的桶，角色因具体的操作而异
	- 查找词条时，被视作“必不匹配的非空桶”，试探链在此得以延续
	- 插入词条时，被视作“必然匹配的空闲桶”，可以用来存放新词条

![[70-Hash-lazy-removal.png]]

```
/**
 * 沿关键码k的试探链(线性试探)，找到与之匹配的桶；
 **/
template <typename K, typename V>
Rank Hashtable<K, V>::probe4Hit( const K& k ) {
	for ( Rank r = hashCode( k ) % M;; r = ( r + 1 ) % M ) //按除余法确定起点，线性试探
	    if ( !ht[r] && !removed->test( r ) || ht[r] && ( k == ht[r]->key ) ) //跳过懒惰删除的桶
	        return r; //只要N+L < M，迟早能终止
}

/**
 * 沿关键码k的试探链，找到首个可用空桶；
 **/
template <typename K, typename V>
Rank Hashtable<K, V>::probe4Free( const K& k ) {
	for ( Rank r = hashCode( k ) % M;; r = ( r + 1 ) % M ) //按除余法确定起点，线性试探
	    if ( !ht[r] ) //只要有空桶（无论是否带有懒惰删除标志）
	        return r; //迟早能找到
}
```

#### 重散列
```
// rehash
/**
 * 重散列：空桶太少时对散列表重新整理：
 * 扩容，再将词条逐一移入新表
 * 散列函数的定址与表长M直接相关，故不可简单地批量复制原桶数组
 **/
template <typename K, typename V>
void Hashtable<K, V>::rehash() {
	Rank oldM = M; Entry<K, V>** oldHt = ht;
	M = primeNLT( 4 * N, 1048576, PRIME_TABLE ); //容量至少加倍（若懒惰删除很多，可能反而缩容）
	ht = new Entry<K, V>*[M]; N = 0; memset( ht, 0, sizeof( Entry<K, V>* ) * M ); //初始化桶数组
	release( removed ); removed = new Bitmap( M ); //懒惰删除标记
   
	for ( Rank i = 0; i < oldM; i++ ) //扫描原表
	    if ( oldHt[i] ) //将每个非空桶中的词条
	        put( oldHt[i]->key, oldHt[i]->value ); //转入新表
	release( oldHt ); //释放――因所有词条均已转移，故只需释放桶数组本身
}

Rank primeNLT( Rank c, Rank n, char* file ) { //根据file文件中的记录，在[c, n)内取最小的素数
   Bitmap B( file, n ); // file已经按位图格式记录了n以内的所有素数，因此只要
   while ( c < n ) //从c开始，逐位地
      if ( B.test( c ) ) c++; //测试，即可
      else return c; //返回首个发现的素数
   return c; //若没有这样的素数，返回n（实用中不能如此简化处理）
}

template <typename K, typename V> struct Entry { //词条模板类
   K key; V value; //关键码、数值
   Entry( K k = K(), V v = V() ) : key( k ), value( v ){}; //默认构造函数
   Entry( Entry<K, V> const& e ) : key( e.key ), value( e.value ){}; //基于克隆的构造函数
   bool operator<( Entry<K, V> const& e ) { return key < e.key; } //比较器：小于
   bool operator>( Entry<K, V> const& e ) { return key > e.key; } //比较器：大于
   bool operator==( Entry<K, V> const& e ) { return key == e.key; } //判等器：等于
   bool operator!=( Entry<K, V> const& e ) { return key != e.key; } //判等器：不等于
}; //得益于比较器和判等器，从此往后，不必严格区分词条及其对应的关键码
```

插入：
```
template <typename K, typename V>
bool Hashtable<K, V>::put( K k, V v ) { //散列表词条插入
	if ( ht[ probe4Hit( k ) ] ) return false; //雷同元素不必重复插入
	Rank r = probe4Free( k ); //为新词条找个空桶（只要装填因子控制得当，必然成功）
	ht[ r ] = new Entry<K, V>( k, v ); ++N; //插入
	if ( removed->test( r ) ) removed->clear( r );  //懒惰删除标记
	if ( (N + removed->size())*2 > M ) rehash(); //若装填因子高于50%，重散列
	return true;
}
```

删除：
```
template <typename K, typename V> bool Hashtable<K, V>::remove( K k ) { //散列表词条删除算法
	int r = probe4Hit( k ); if ( !ht[r] ) return false; //确认目标词条确实存在
	release( ht[r] ); ht[r] = NULL; --N; //清除目标词条
	removed->set(r); //更新标记、计数器
	if ( removed->size() > 3*N ) rehash(); //若懒惰删除标记过多，重散列
	return true;
}
```

#### 平方试探
Quadratic Probing:
- 以平方数为距离，确定下一试探桶的位置：
- ![[70-Hash-quadratic-prob.png]]
- 能够缓解数据聚集现象，试探链上各桶之间的距离线性递增，一旦冲突能很快离开冲突局部；
- ![[70-Hash-quadraatic-probing.png]]
- 但是未必能找到所有空桶：表长为素数时，对于装填因子λ<0.5 就一定能找出，否则不行：
	- 考虑{0,1,2,3,4,...,}^2 % 12 = {0,1,4,9},
	- {0,1,2,3,4,...,}^2 % 11 = {0,1,4,9,5,3}
	- ![[70-Hash-prime-composite-quadratic.png]]
	- M 为合数，则 n^2 % M 的取值小于 $\lceil \frac{M}{2}\rceil$ 种
	- M 为素数，则 n^2 % M 恰有 $\lceil \frac{M}{2}\rceil$ 种取值，且由试探链的前 $\lceil \frac{M}{2}\rceil$ 项取遍；

因此，试探链的前缀必须足够长，且无重复：
反证：
- 假设存在 $0\le a<b<\lceil \frac{M}{2}\rceil$ ，使得沿着试探链，第 a 项和第 b 项彼此冲突
- 于是：a^2 和 b^2 自然关于 M 同余，亦即 $a^{2} \equiv b^{2} (mod M),b^{2}-a^{2}=(b+a)(b-a)\equiv 0(mod M)$
- 然而，$0<b-a\le b+a<\lceil \frac{M}{2}\rceil+(\lceil \frac{M}{2}\rceil-1)\le \lceil \frac{M}{2}\rceil+\lfloor \frac{M}{2}\rfloor=M$ 无论 b-a 还是 b+a 都不可能整除 M 
- 那么，另一半的桶，可否也利用起来呢... (再加一层线性散列)

#### 双向平方试探
策略：交替地沿两个方向试探，均是平方的距离。
![[70-Hash-bidirectional-square-probes.png]]

![[70-Hash-bidirectional-square-probe-instance.png]]
- 正向和反向的子试探链，各自包含 $\lceil \frac{M}{2}\rceil$ 个互异的桶 ：
- $-\lfloor \frac{M}{2}\rfloor,...,-3,-2,-1,0,1,2,3,...,\lfloor \frac{M}{2}\rfloor$ 
- 除了起点 0，且表长 M 为素数并可以表示为 M=4k+3 的形式，就可以保证试探链的前 M 项均互异
	- Two-Square Theorem of Fermat: 整数 p 不能表示为一对整数的平方和，当且仅当 $p\equiv 3 (mod 4)$ 
	- 注意到 $(u^{2}+v^{2})(s^{2}+t^{2})=(us+vt)^{2}+(ut-vs)^{2}=(us-vt)^{2}+(ut+vs)^{2}$
	- 可以推知，自然数 n 可以表示为一对整数的平方和，当且仅当 n 的每一 M=4k+3 类素因子均为偶数次方

#### 双散列
预先约定第二个散列函数 hash2 (key, i)，在发生冲突时，由冲突确定偏移量，确定下一试探的位置：$\left[hash(key)+\sum\limits_{i=1}^{k}hash2(key,i)\right] \% M$

比较好用的试探可以是：
- 线性试探：$hash2(key,i)\equiv 1$ 
- 平方试探：$hash2(key,i)=2i-1$ 
- 更一般的，偏移增量同时与 key 相关

![[70-Hash-double-hash.png]]


