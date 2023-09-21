## 应用情景
文件编码时，字符通过二进制编码，组成数据文件的字符来自一个字符集Σ，字符通过赋值为互异的二进制串。因此随着字符集的增加，二进制串势必越来越长。

文件的大小=字符数量 x 各字符编码时的二进制串的长短。因此如何对各字符编码，使得文件最小？
## PFC 编码
![[50-Tree-pfc.png]]
- 将字符集 Σ 中的字符组织成一棵二叉树，以 0/1 表示左/右孩子，各字符 x 分别存放于对应的叶子 v (x)中
- 字符 x 的编码串 $rps(v(x))=rps(x)$ 则由根到叶子的通路唯一确定；
- 字符编码不是等长，且不同字符的编码互不为前缀（Prefix-Free Code）（因为所有字符都在叶节点，而不是中间节点），故没有歧义；
- 但是缺点是，如果使用频繁的字符放在最底层，意味着这个字符本身占用的“长”二进制串将反复调用，使得文件大小不是最优。
- 考查平均编码长度：$ald(T)=\sum\limits_{x\in\Sigma}\frac{depth(v(x))}{|\Sigma|}$
- 最优编码树 $T_{opt}$ 即为使 $ald()$ 最小者，显然它一定存在且未必唯一。

## PFC 解码
反过来，依据 PFC 编码树可便捷地完成编码串的解码:
1. PFC 编码中的编码树为例，设对编码串"101001100"解码。从前向后扫描该串，同时在树中相应移动。
2. 起始时从树根出发，视各比特位的取值相应地向左或右深入下一层，直到抵达叶节点。比如，在扫描过第 1 位 "1"后将抵达叶节点'M'。此时，可以输出其对应的字符'M'，然后重新回到树根，并继续扫描编码串的剩余部分。
3. 再经过接下来的 3 位"010"后将抵达叶节点'A'，同样地输出字符'A' 并回到树根。
4. 如此迭代，即可无歧义地解析出原文中的所有字符。

实际上，这一解码过程甚至可以在二进制编码串的接收过程中实时进行，而不必等到所有比特位都到达之后才开始，因此这类算法属于==在线算法==。

## 最优编码树
叶子只能出现在倒数两层以内——否则，通过节点交换即可降低总的编码长度：
![[50-Tree-PFC-opt.png]]

- $\forall v\in T_{opt}, degree(v)=0\ \iff\ depth(v)\ge depth(T_{opt})-1$ (当且仅当的等价关系)
- 字符的出现概率或频度不尽相同，甚至，往往相差极大... 已知各字符的期望频率，如何构造最优编码树？

## 最优带权编码树
- 首先明确，文件长度 $\propto$ 平均带权深度 $weight_ald()=\sum\limits_{x}rps(x)\times w(x)$
- 如果综合考虑带权情况，完全树未必是最优编码树：
![[50-Tree-wald.png]]

- 因此，通过调整频率不同的字符所在编码树的深度，可以降低 wald (T)——频率高的字符，放在编码树的高处；

## Huffman 树
- Huffman 的贪心策略：频率低的字符优先引入，其位置亦更低：
	- 为每个字符创建一棵单节点的树，组成森林 F
	- 按照出现频率，对所有树排序
	- while ( F 中的树不止一棵 )
		- 取出==频率最小的两棵树==：T1 和 T2
		- 将它们==合并成一棵新树== T，并令：
			- lc (T) = T1 且 rc (T) = T2
			- w( root(T) ) = w( root(T1) ) + w( root(T2) )

- 尽管贪心策略未必总能得到最优解，但非常幸运，如上算法的确能够得到最优编码树之一。

![[50-Tree-huffman-pfc.png]]

## Huffman 树的特性
1. 双子性：
	- 每个内部节点都有两个孩子——==真二叉树==
	- 否则将 1 度节点替换成唯一的孩子，得到的新树 wald 更小（注意叶节点才保存字符信息）：
	- ![[50-Tree-huffman-true-tree.png]]
2. 不唯一性：
	- 对任一内部节点而言，左右子树互换后 wald 不变，故 Huffman 算法中兄弟子树的次序若随机选取，则可能出现歧义——不同的最优带权编码树；
	- 为了消除这种歧义，可以要求左子树的频率不低于右子树；
	- ![[50-Tree-huffman-!unique.png]]
3. 层次性：
	- 出现频率最低的字符 x 和 y ，必在某棵最优编码树中处于最底层，且互为兄弟；
	- 否则，任取一棵最优编码树，并在其最底层任取一对兄弟 a 和 b，a 和 x 、 b 和 y 交换之后，wald 也不会增加
	- ![[50-Tree-huffman-layer.png]]

## Huffman 树的最优性证明
对字符集|Σ|做数学归纳可证：Huffman 算法所生成的，必是一棵最优编码树！
- |Σ| = 2 时显然正确；
- 设算法在|Σ|<n 时均正确，则设|Σ|=n，取Σ中频率最低的 x、y，并不妨设其互为兄弟：
- 令 $\Sigma^{'}=(\Sigma \\ \{x,y\} \cup \{z\}, w(z)=w(x)+w(y)$，
- ![[50-Tree-huffman-justify.png]]
- 则对 Σ' 的任一编码树 T'，只要为 z 添加孩子 x 和 y 就可以得到Σ的一棵编码树 T，且 $wd(T)-wd(T^{'})=w(x)+w(y)=w(z)$
- 因此可见，如此对应的 T 和 T'，wd 之差与 T 的具体形态无关，因此只要 T'是 Σ' 的最优编码树，则 T 也必然是Σ的最优编码树之一；
- 事实上 Huffman 的每一步迭代，都相当于从最优编码树 T 转入另一棵最优编码树 T'

## Huffman 树的实现
```
#define N_CHAR (0x80 - 0x20) //仅以可打印字符为例

struct HuffChar { //Huffman（超）字符
	char ch; unsigned int weight; //字符、频率
	HuffChar ( char c = '^', unsigned int w = 0 ) : ch ( c ), weight ( w ) {};
	
	bool operator< ( HuffChar const& hc ) { return weight > hc.weight; } //比较器
	bool operator== ( HuffChar const& hc ) { return weight == hc.weight; } //判等器
};

// Huffman(子)树、森林
using HuffTree = BinTree<HuffChar>;

using HuffForest = List<HuffTree*>;

// 构造编码树：反复合并二叉树
HuffTree* generateTree( HuffForest * forest ) { 
	//Huffman编码算法
	while ( 1 < forest->size() ) { //反复迭代，直至森林中仅含一棵树
		HuffTree *T1 = minHChar( forest ), *T2 = minHChar( forest ); 
		HuffTree *S = new HuffTree(); //创建新树，然后合并T1和T2
		S->insert( HuffChar('^', T1->root()->data.weight + T2->root()->data.weight) );
		S->attach( T1, S->root() ); S->attach( S->root(), T2 );
		forest->insertAsLast( S ); //合并之后，重新插回森林
	} //assert: 森林中最终唯一的那棵树，即Huffman编码树
	return forest->first()->data; //故直接返回之
}

// 查找最小(超)字符：遍历List/Vector
HuffTree* minHChar( HuffForest* forest ) { 
	//此版本仅达到O(n)，故整体为O(n^2)
	ListNodePosi<HuffTree*> m = forest->first(); //从首节点出发，遍历所有节点
	for ( ListNodePosi<HuffTree*> p = m->succ; forest->valid( p ); p = p->succ )
		if( m->data->root()->data.weight > p->data->root()->data.weight ) //不断更新
			m = p; //找到最小节点（所对应的Huffman子树）
	return forest->remove( m ); //从森林中取出该子树，并返回
} //Huffman编码的整体效率，直接决定于minHChar()的效率
```

```
// 构造编码表：遍历二叉树
#include "Hashtable.h" //用HashTable实现
using HuffTable = Hashtable< char, char* >; //Huffman编码表
static void generateCT //通过遍历获取各字符的编码
( Bitmap* code, int length, HuffTable* table, BinNodePosi<HuffChar> v ) {
	if ( IsLeaf( * v ) ) //若是叶节点（还有多种方法可以判断）
	{ 
		table->put( v->data.ch, code->bits2string( length ) ); 
		return; 
	}
	if ( HasLChild( * v ) ) //Left = 0，深入遍历
	{ 
		code->clear( length ); 
		generateCT( code, length + 1, table, v->lc ); 
	}
	if ( HasRChild( * v ) ) //Right = 1
	{ 
		code->set( length ); 
		generateCT( code, length + 1, table, v->rc );
	}
} //总体O(n)
```

## Huffman 树构造的改进方案
1. 非升序向量：O (n^2)
	1. 初始化时，通过排序得到一个非升序向量 //O (nlogn) 
	2. 每次（从后端）取出频率最低的两个节点 //O (1)
	3. 将合并得到的新树插入向量，并保持有序 //O (n)
2. 非将序列表：O (n^2)
	1. 初始化时，通过排序得到一个非降序列表 //O (nlogn)
	2. 每次（从前端）取出频率最低的两个节点 //O (1)
	3. 将合并得到的新树插入列表，并保持有序 //O (n)
3. 优先级队列：O (nlogn)
	1. 初始化时，将所有树组织为一个优先级队列（第 12 章）//O (n) 
	2. 取出频率最低的两个节点，合并得到的新树插入队列 //O (logn) + O (logn)
4. 按频率排序后使用栈和队列：O (nlogn)
	1. 所有字符按频率非升序入栈 //O (nlogn)
	2. 维护另一（有序）队列... //O(n)
![[50-Tree-huffman-instance.png]]
![[50-Tree-huffman-improve-instance2.png]]