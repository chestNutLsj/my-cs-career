## Splay Tree
### 局部性
计算机基础理论之一——局部性原理：在 BST 中刚被访问过的节点，极有可能再次被访问，并且可能下一个访问的节点就在刚访问过的节点的附近。

对 AVL 树的连续 m 次查找，共需 O (mlogn)时间，当 m 接近于 n 或超越 n 的数量级时，显然非常低效。思考 [[31-Exercise#3-6 自调整列表|自调整链表]]的思路——节点一旦被访问，随即移动到最前端。

将这个思路应用到 BST——节点一旦被访问，调整至树根。那么如何进行调整呢？最直接地，逐层旋转调整，一步步爬升至根：
![[60C-BBST-Splay-tree-rotate-layer-by-layer.png]]

显然伸展（旋转、攀爬）的效率取决于树的初始形态和查找节点的访问次序，考虑最坏情况——单侧链的 BST，旋转次数是周期性的算术级数，每一周期为 Ω(n^2)，分摊为 Ω(n)：
![[图08-02.简易伸展树的最坏情况.png]]

### 双层伸展
核心操作：反复考查祖孙三代 g~p~v，根据相对位置经过两次旋转，使得 v 上升两层成为子树的根。

#### zig-zag/zag-zig
此时 v 按中序遍历次序为居中，若要成为根，则需进行两次方向不同的旋转：
![[60C-BBST-Splay-tree-zig-zag.png]]
- 看起来与逐层调整没什么区别...
- 但其实精髓不在这里，因为这种情况里 v 想要上升为根，必须如此旋转。

#### zig-zig/zag-zag
此时 v 在中序遍历居左或居右的位置，若要成为根，则需进行两次方向相同的旋转，但是问题在于从 v 旋转还是从 g 旋转：
![[60C-BBST-Splay-tree-zig-zig.png]]
1. 从 v 开始一层层向上旋转，如图第一行（逐层伸展）；
2. 从 g 开始一层层向下旋转，如图第二行（双层伸展）；

在这个局部很难看出差异，但局部的细微差异导致全局的不同——tarjan 双层伸展：
![[图08-06.双层调整策略的高度折半效果.png]]

![[60C-BBST-Splay-tree-double-layer-rotate.png]]

可以看到，节点访问之后，对应路径的长度随即折半，使得最坏情况不致于持续发生。伸展操作的分摊时间仅需 O (logn)时间！

在习题集 [[61-Exercise#8-2 Splay Tree 操作的分摊时间复杂度为 O (logn)|8-2 Splay Tree的各操作分摊时间复杂度为O(logn)]] 中有详细证明。

最后不要忘记，v 只有父亲没有祖父的情况：v 在操作的最后，仅次于根节点处：
![[60C-BBST-Splay-tree-zigzag.png]]
此时必有 v.parent () == T.root ()，只需要单次旋转即可，并且每轮伸展最多发生一次。

### 伸展实现
```
// 伸展树的接口
template <typename T> 
class Splay : public BST<T> { //由BST派生
protected:
	BinNodePosi<T> splay( BinNodePosi<T> v ); //将v伸展至根
public: //伸展树的查找也会引起整树的结构调整，故search()也需重写
	BinNodePosi<T> & search( const T & e ); //查找（重写）
	BinNodePosi<T> insert( const T & e ); //插入（重写）
	bool remove( const T & e ); //删除（重写）
};
```

```
// 伸展算法
template <typename NodePosi> inline 
//在节点*p与*lc（可能为空）之间建立父（左）子关系
void attachAsLC( NodePosi lc, NodePosi p ) { 
	p->lc = lc; 
	if ( lc ) lc->parent = p; 
}

template <typename NodePosi> inline 
//在节点*p与*rc（可能为空）之间建立父（右）子关系
void attachAsRC( NodePosi p, NodePosi rc ) { 
	p->rc = rc; 
	if ( rc ) rc->parent = p; 
}

template <typename T> 
//Splay树伸展算法：从节点v出发逐层伸展
BinNodePosi<T> Splay<T>::splay( BinNodePosi<T> v ) { 
	// v为因最近访问而需伸展的节点位置
    if ( !v ) return NULL; 
    BinNodePosi<T> p; BinNodePosi<T> g; //*v的父亲与祖父
    while ( ( p = v->parent ) && ( g = p->parent ) ) { 
		//自下而上，反复对*v做双层伸展
	    BinNodePosi<T> gg = g->parent; //每轮之后*v都以原曾祖父（great-grand parent）为父
	    if ( IsLChild( *v ) )
		    if ( IsLChild( *p ) ) { // zig-zig
	            attachAsLC( p->rc, g ); 
	            attachAsLC( v->rc, p );
	            attachAsRC( p, g ); 
	            attachAsRC( v, p );
        } else { //zig-zag
            attachAsLC( v->rc, p ); 
            attachAsRC( g, v->lc );
            attachAsLC( g, v ); 
            attachAsRC( v, p );
        }
	    else
	        if ( IsRChild ( *p ) ) { //zag-zag
	            attachAsRC( g, p->lc ); 
	            attachAsRC( p, v->lc );
	            attachAsLC( g, p ); 
	            attachAsLC( p, v );
         } else { //zag-zig
	            attachAsRC( p, v->lc ); 
	            attachAsLC( v->rc, g );
	            attachAsRC( v, g ); 
	            attachAsLC( p, v );
         }
	    if ( !gg ) v->parent = NULL; //若*v原先的曾祖父*gg不存在，则*v现在应为树根
	    else //否则，*gg此后应该以*v作为左或右孩子
         ( g == gg->lc ) ? attachAsLC( v, gg ) : attachAsRC( gg, v );
		updateHeight( g ); 
		updateHeight( p ); 
		updateHeight( v );
    } //双层伸展结束时，必有g == NULL，但p可能非空
	
	if ( p = v->parent ) { 
	//若p果真非空，则额外再做一次单旋
		if ( IsLChild( *v ) ) { 
			attachAsLC( v->rc, p ); 
			attachAsRC( v, p ); 
		} else { 
			attachAsRC ( p, v->lc ); 
			attachAsLC ( p, v ); 
		}
	    updateHeight( p ); 
	    updateHeight( v );
    }
    v->parent = NULL; 
    return v;
} //调整之后新树根应为被伸展的节点，故返回该节点的位置以便上层函数更新树根

```

![[60C-BBST-Splay-tree-splay-instance.png]]

### 查找
```
template <typename T> 
BinNodePosi<T> & Splay<T>::search( const T & e ) {
	// 调用标准BST的内部接口定位目标节点
	BinNodePosi<T> p = BST<T>::search( e );
	// 无论成功与否，最后被访问的节点都将伸展至根
	_root = splay( p ? p : _hot ); //成功、失败
	// 总是返回根节点
	return _root;
}
```

伸展树的查找，与常规 BST::search()不同：很可能会改变树的拓扑结构，==不再属于静态操作==。

### 插入
除了直观地先查找、再插入、最后伸展至树根，我们可以直接在查找失败时直接在树根附近插入新节点：
![[图08-08.伸展树的节点插入.png]]

```
template <typename T> 
BinNodePosi<T> Splay<T>::insert( const T & e ) {
	if ( !_root ) { 
		_size = 1; 
		return _root = new BinNode<T>( e ); 
	} //原树为空
	
	BinNodePosi<T> t = search( e ); 
	if ( e == t->data ) return t; //t若存在，伸展至根
	if ( t->data < e ) { 
		//在右侧嫁接（rc或为空，lc == t必非空）
		t->parent = _root = new BinNode<T>( e, NULL, t, t->rc ); //lc==t必非空
		if ( t->rc ) { //rc或为空
			t->rc->parent = _root; 
			t->rc = NULL; 
		}
	} else { 
	//e < t->data，在左侧嫁接（lc或为空，rc == t必非空）
		t->parent = _root = new BinNode<T>( e, NULL, t->lc, t );
		if ( t->lc ) { 
			t->lc->parent = _root; 
			t->lc = NULL; 
		}
	}
	
	_size++; 
	updateHeightAbove( t ); 
	return _root; //更新规模及t与_root的高度，插入成功
} //无论如何，返回时总有_root->data == e
```

### 删除
直观地，查找到对应节点后删除之，再将_hot 伸展至根。可以考虑先伸展至树根，再于树根附近直接摘除目标节点：
![[图08-09.伸展树的节点删除.png]]

```
template <typename T> 
bool Splay<T>::remove( const T & e ) {
	if ( !_root || ( e != search( e )->data ) ) 
		return false; 
		
	//若目标存在，则伸展至根
	BinNodePosi<T> L = _root->lc, R = _root->rc; release(_root); //记下子树后，释放之
	if ( !R ) { //若R空
		if ( L ) L->parent = NULL; _root = L; //则L即是余树
	} else { //否则
		_root = R; R->parent = NULL; search( e ); //在R中再找e：注定失败，但最小节点必
		if (L) L->parent = _root; _root->lc = L; //伸展至根，故可令其以L作为左子树
	}
	_size--; if ( _root ) updateHeight( _root ); //更新记录
	return true; //删除成功
}
```

### 性能分析
Splay Tree 的优点：
- 无需记录高度或平衡因子；
- 编程实现简单——优于 AVL 树；
- 分摊复杂度 O (logn) ——与 AVL 树相当 (不考虑局部性时才会降低至此，因此在多次、具有局部性的查找中效率突破了 O (logn) )
- 局部性强、缓存命中率极高时（即下图中 k << n << m 时，m 是查找次数）
	- ![[60C-BBST-Splay-tree-locality.png]]
	- 效率甚至可以更高——自适应的 O (logk) 
	- 任何连续的 m 次查找，仅需 O (mlogk+nlogn) 时间
	- 若反复地顺序访问任一子集，分摊成本仅为常数；

Splay Tree 的缺点：
- 不能杜绝单次最坏情况，不适用于对效率敏感的场合
- 复杂度的分析稍嫌复杂——好在有初等的证明：势能分析

### 势能分摊分析
#### 势能定义
任何时刻的任何一棵伸展树 S，都可以假想地认为具有势能：
$$
\Phi(S)=\log(\prod\limits_{v\in S}size(v)) =\sum\limits_{v\in S}\log(size(v))=\sum\limits_{v\in S}rank(v)=\sum\limits_{v\in S}log V
$$

符合直觉的：越平衡的树，势能越小（树高越低）；越倾侧的树，势能越大（树高越高）
- 单链的势能：$\Phi(S)=\log n!=O(n\log n)$
- 满树的势能： 
$$
\begin{aligned}
\Phi(S)&=\log\prod\limits_{d=0}^{h}(2^{h-d+1}-1)^{2^{d}} \\
&\le \log\prod\limits_{d=0}^{h}(2^{h-d+1})^{2^{d}}\\
&=\log\prod\limits_{d=0}^{h}2^{{(h-d+1)}\cdot{2^{d}}}\\
&=\sum\limits_{d=0}^{h}(h-d+1)\cdot 2^{d}\\
&=(h+1)\sum\limits_{d=0}^{h}2^{d}-\sum\limits_{d=0}^{h}d\cdot 2^{d}\\
&=(h+1)\cdot(2^{h+1}-1)-[(h-1)\cdot2^{h+1}+2]\\
&=2^{h+2}-h-3\\
&=O(n)
\end{aligned} 

$$
#### 