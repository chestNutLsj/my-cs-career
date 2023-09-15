## 树的特征、相关概念定义
1. 半线性：确定某种次序后，具有线性特征；
2. 描述：T=(V; E) ，节点数 n=|V|，边数 e=|E|
3. 有根树：任一节点 $r \in V$ 指定为根，树都称为有根树：
	- ![[50-Tree-subtree.png]]
4. 节点和边的关系：
	1. $r_i$ 是 r 的孩子，$r_i$ 互称兄弟，
	2. r 称为父亲，$d=degree(r)$ 为 r 的 (出)度
	3. 可以证明，$e=\sum\limits_{v\in V}degree(v)=n-1=\Theta(n)$
	4. 指定 $T_i$ 为 T 的第 i 棵子树，$r_i$ 为 r 的第 i 个孩子，则称树 T 为有序树
5. 路径/通路 path：节点集 V 中 k+1 个节点，通过 k 条边相连
	1. 通路 $\pi=\{(v_{0},v_{1}),(v_{1},v_{2}),...,(v_{k-1},v_{k})\}$
	2. 路径长度：所含边数 $|\pi|=k$
	3. 环路 loop/cycle：$v_{k}=v_{0}$
6. 极小连通图：节点之间均有路径，且边数最少的情况
7. 极大无环图：不存在环路，且边数最多的情况
8. 树任一节点都与根存在唯一路径：path (v, r)=path (v)，
	1. 按照 |path (v)|的大小可以将节点作等价类划分——相同深度
	2. 节点 v 的深度 depth (v)=|path (v)|
	3. path (v)上节点，均为 v 的祖先（ancestor）, v 是它们的后代（descendent）
	4. 除自身以外，都是真（proper）祖先/后代
9. 根节点 r 是所有节点的公共祖先，深度为 0
	1. 没有后代的节点称为叶子
	2. 叶子深度的最大者称为树的高度 height (v)=height (subtree (v))
	3. 空树的高度取作-1，
	4. 则 depth (v)+height (v)<=height (T) （==当且仅当 v 是最深路径上的节点时，取等号==）
	5. ![[50-Tree-height-depth-relation.png]]

## 树的 ADT 和表示
### ADT
- root () 根节点
- parent () 父节点
- firstChild () 长子
- nextSibling () 兄弟
- insert (i, e) 将 e 作为第 i 个孩子插入
- remove (i) 删除第 i 个孩子（及其后代）
- traverse () 遍历

### 父亲-孩子表示法
- 父亲表示：节点组织为一个序列，各自记录：data 本身信息、parent 父节点的秩或位置
	- 树根的父亲为其自身：R ~ parent (4) = 4（下面是旧图，按新 ppt 指向自身来）
	- ![[图05-03.多叉树的“父节点”表示法.png]]
	- 性能：
		- 空间占用 O (n)，
		- parent ()为 O (1),
		- root ()为 O (depth (v))
		- firstChild () 无法实现
		- nextSibling () 无法实现
- 孩子表示：同一节点的所有孩子，各成一个序列，各序列的长度，即对应节点的度数：
	- ![[图05-04.多叉树的“孩子节点”表示法.png]]
	- 性能：
		- parent ()无法实现
		- root ()无法实现
		- firstChild ()为 O (1)
		- nextSibling ()为 O (1)
- 因此综合考虑，使用父亲-孩子表示法：
	- ![[图05-05.多叉树的“父节点+孩子节点”表示法.png]]
	- 性能：
		- parent ()为 O (1)
		- root ()为 O (depth)
		- firstChild ()为 O (1)
		- nextSibling ()为 O (1)

### 长子-兄弟表示法
- 每个节点只设置 3 个引用：parent ()、firstChild ()和 nextSibling ()
- ![[图05-06.多叉树的“长子+兄弟”表示法（在(b)中，长子和兄弟指针分别以垂直实线和水平虚线示意）.png]]
- 性能：
	- parent ()为 O (1)
	- root ()为 O (层次遍历中的次序)
	- firstChild ()为 O (1)
	- nextSibling ()为 O (1)
	- O (degree (v)+1)的时间可以遍历所有 v 的孩子

## 二叉树
### 特点与类别
1. Binary Tree 的度数不超过 2，孩子左右区分
2. 多叉树都可以通过长子兄弟表示法转化为二叉树：长子~左孩子，兄弟~右孩子
3. 深度为 k 的节点，至多有 2^k 个
4. n 个节点，高度 h 的二叉树满足：$h+1\le n\le 2^{h+1}-1$
5. 度数、节点数、边数的关系： ^b26f71
	1. 设度数为 0、1、2 的节点各有 $n_0$， $n_1$， $n_2$ 个
	2. 则边数 e=n-1= $n_1+2n_2$，
		-  ![[50-Tree-edge-node-num-relation.png]]
	3. 叶节点数 $n_0$ = $n_2+1$
		1. $n_1$ 数量与 $n_0$ 数量无关，因为 $n_1$ 总可以回退到 $n_2$ 中
		2. h=0 时，$n_0$=1=$n_2$+1，此后 $n_0$ 与 $n_2$ 同步递增；
	4. 节点数 n= $n_0$ + $n_1$ + $n_2$ = $n_1$ + $2n_2$
	5. $n_1$ =0 时，所有节点的度数都是偶数，此时 e=$2n_2$, $n_0$=$n_2+1$=(n+1)/2
5. 满二叉树：$n=2^{k+1}-1$，每一层都是 2^k 个节点
6. 真二叉树：引入 $n_1+2n_0$ 个外部节点，将所有节点度数转换为 2
	- ![[50-Tree-proper-binary-tree.png]]

### ADT: BinNode
```
template <typename T> using BinNodePosi = BinNode<T>*; //节点位置
template <typename T> struct BinNode {
	BinNodePosi<T> parent, lc, rc; //父亲、孩子
	T data; Rank height, npl; Rank size(); //高度、npl、子树规模
	BinNodePosi<T> insertAsLC( T const & ); //作为左孩子插入新节点
	BinNodePosi<T> insertAsRC( T const & ); //作为右孩子插入新节点
	BinNodePosi<T> succ(); //（中序遍历意义下）当前节点的直接后继
	template <typename VST> void travLevel( VST & ); //层次遍历
	template <typename VST> void travPre( VST & ); //先序遍历
	template <typename VST> void travIn( VST & ); //中序遍历
	template <typename VST> void travPost( VST & ); //后序遍历
};
```

![[50-Tree-binnode.png]]

引入新节点：
```
template <typename T>
BinNodePosi<T> BinNode<T>::insertAsLC( T const & e )
	{ return lc = new BinNode( e, this ); }
	
template <typename T>
BinNodePosi<T> BinNode<T>::insertAsRC( T const & e )
	{ return rc = new BinNode( e, this ); }
```
![[50-Tree-insertBinNode.png]]

### ADT: BinTree
```
template <typename T> class BinTree {
protected: 
	Rank _size; //规模
	BinNodePosi<T> _root; //根节点
	virtual Rank updateHeight( BinNodePosi<T> x ); //更新节点x的高度
	void updateHeightAbove( BinNodePosi<T> x ); //更新x及祖先的高度
public: 
	Rank size() const { return _size; } //规模
	bool empty() const { return !_root; } //判空
	BinNodePosi<T> root() const { return _root; } //树根
	/* ... 子树接入、删除和分离接口；遍历接口 ... */
}
```

#### Insert
```
// Insert node
BinNodePosi<T> BinTree<T>::insert( BinNodePosi<T> x, T const & e ); //作为右孩子

BinNodePosi<T> BinTree<T>::insert( T const & e, BinNodePosi<T> x ) { //作为左孩子
	_size++;
	x->insertAsLC( e );
	updateHeightAbove( x );
	return x->lc;
}
```
![[50-Tree-insert-node.png]]

```
// 插入子树
BinNodePosi<T> BinTree<T>::attach( BinTree<T>* &S, BinNodePosi<T> x ); //接入左子树

BinNodePosi<T> BinTree<T>::attach( BinNodePosi<T> x, BinTree<T>* &S ) { //接入右子树
	if ( x->rc = S->_root )
		x->rc->parent = x;
	_size += S->_size;
	updateHeightAbove(x);
	S->_root = NULL; S->_size = 0;
	release(S); S = NULL;
	return x;
}
```
![[50-Tree-insert-subtree.png]]

### Update height
```
// 更新高度
#define stature(p) ((int)((p)?(p)-height:-1)) //空树高度为-1
template <typename T> Rank BinTree<T>::updateHeight( BinNodePosi<T> x ) //更新节点x高度
{ 
	return x->height = 1 + max( stature( x->lc ), stature( x->rc ) ); 
} //具体规则，因树而异，此处为常规二叉树，O(1)

template <typename T> void BinTree<T>::updateHeightAbove( BinNodePosi<T> x ) //更新高度
{ 
	while ( x ) { 
		updateHeight( x );
		x = x->parent; 
	} 
} //从x出发，覆盖历代祖先。可优化
```

### Secede subtree
```
template <typename T> BinTree<T>* BinTree<T>::secede( BinNodePosi<T> x ) {
	FromParentTo( * x ) = NULL; updateHeightAbove( x->parent );
	// 以上与BinTree<T>::remove()一致；以下还需对分离出来的子树重新封装
	BinTree<T> * S = new BinTree<T>; //创建空树
	S->_root = x; x->parent = NULL; //新树以x为根
	S->_size = x->size(); _size -= S->_size; //更新规模
	return S; //返回封装后的子树
}
```

## 遍历
![[50-Tree-traverse.png]]

```
template <typename T>
Rank BinNode<T>::size() { //后代总数,亦为子树规模
	Rank s = 1; //计入本身
	if (lc) s += lc->size(); //递归计入左子树规模
	if (rc) s += rc->size(); //递归计入右子树规模
	return s;
} //懒惰策略，O( n = |size| )
```
### 先序遍历
#### 递归版
```
// recursive version
template <typename T, typename VST> 
void traverse( BinNodePosi<T> x, VST & visit ) {
	if ( ! x ) return;
		visit( x->data );
	traverse( x->lc, visit );
	traverse( x->rc, visit );
} //O(n)

```
- 制约：使用默认的 Call Stack，允许的递归深度有限
- 如何化尾递归为迭代？
![[50-Tree-preorder.png]]

#### 先序遍历特点——藤缠树
![[图05-32.先序遍历过程：先沿左侧通路自顶而下访问沿途节点，再自底而上依次遍历这些节点的右子树.png]]
- 沿着左侧藤，整个遍历过程可分解为：
	- 自上而下访问藤上节点，
	- 再逆转方向，自下而上遍历各右子树，各右子树的遍历彼此独立自成子任务；

#### 迭代版
```
template <typename T, typename VST>
void travPre_I2( BinNodePosi<T> x, VST & visit ) {
	Stack < BinNodePosi<T> > S; //辅助栈
	while ( true ) { //以右子树为单位，逐批访问节点
		visitAlongVine( x, visit, S ); //访问子树x的藤蔓，各右子树（根）入栈缓冲
		if ( S.empty() ) break; //栈空即退出
		x = S.pop(); //弹出下一右子树（根）
	} //#pop = #push = #visit = O(n) = 分摊O(1)
}

template <typename T, typename VST> static void visitAlongVine
( BinNodePosi<T> x, VST & visit, Stack < BinNodePosi<T> > & S ) { //分摊O(1)
	while ( x ) { //反复地
		visit( x->data ); //访问当前节点
		S.push( x->rc ); //右孩子（右子树）入栈（将来逆序出栈）
		x = x->lc; //沿藤下行
	} //只有右孩子、NULL可能入栈——增加判断以剔除后者，是否值得？
}
```