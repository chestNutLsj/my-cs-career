## 平衡因子
$balFac(v)=height(lc(v))-height(rc(v))$
![[60B-Balanced-BST-balFac.png]]

## 高度与节点数
高度为 h 的 AVL 树，至少包含 $S(h)=fib(h+3)-1$ 个节点。证明如下：
1. 对给定高度 h 、节点数最少的 AVL 树，将其规模记作 S(h)，则有
2. $S(h)=1+S(h-1)+S(h-2)$
3. $S(h)+1=[S(h-1)+1]+[S(h-2)+1]$
4. $fib(h+3)=fib(h+2)+fib(h+1)$

反之同理，n 个节点构成的 AVL 树，高度不超过 O(logn)。

这种节点数最少的 AVL 树的形态是单侧高 1，且高的一侧仅一个节点：
![[60B-Balanced-BST-fib-avl.png]]

![[60B-Balanced-BST-fib-avl2.png]]

## 失衡与复平衡
AVL 树在动态操作（插入、删除）后，可能会破坏平衡性，且==该失衡只发生在被操作节点的祖先上==：
- 插入：从祖父开始，每个祖先都有可能失衡，且可能同时失衡！
- 删除：从父亲开始，每个祖先都有可能失衡，但至多一个！（因为删除只会改变自己分支的高度，而不会影响另一个分支，因此只会影响到父亲的平衡因子）
- 虽然如此，==看起来插入的影响较删除更大，但实际上插入造成的失衡恢复起来比删除简单得多==。

![[60B-Balanced-BST-avl-unbalance.png]]

```
// 涉及AVL树的平衡性的接口
#define Balanced(x) ( stature( (x).lc ) == stature( (x).rc ) ) //理想平衡

#define BalFac(x) (stature( (x).lc ) - stature( (x).rc ) ) //平衡因子

#define AvlBalanced(x) ( ( -2 < BalFac(x) ) && (BalFac(x) < 2 ) ) //AVL平衡条件

template <typename T> 
class AVL : public BST<T> { //由BST派生
public: //BST::search()等接口，可直接沿用
	BinNodePosi<T> insert( const T & ); //插入（重写）
	bool remove( const T & ); //删除（重写）
}
```

复衡的手段就是在 [[60A-Binary_Search_Tree#^fef5a1|局部进行旋转操作]] ：
- 所有旋转都在局部进行，每次只需 O (1)时间；
- 每个深度只需检查并旋转至多一次，故总计 O (logn)次；

## 插入
insert (x)可能造成同时有多个失衡的节点，其中失衡的最低者 g 不低于 x 的祖父。共有两种情况，分别通过一次旋转和两次旋转可以复衡：

1. 单旋：x 在失衡局部的两侧
	- ![[60B-Balanced-BST-avl-rebalance-once.png]]
	- 上图中 x 插入黄色方块中任意一处，镜像情况同理；
	- g 只需经过单次旋转后就能复衡，g 作根节点的子树的高度复原（子树高度不变化）
	- 更高祖先因此复衡，全树复衡；

2. 双旋：x 在失衡局部的中间
	- ![[60B-Balanced-BST-avl-rebalance-twice.png]]
	- 先对失衡节点 g 的孩子（x 插入的子树）进行一次旋转，然后对失衡节点 g 进行一次旋转；
	- 同样，以 g 作根节点的子树的高度复原，更高祖先同时复衡；

```
template <typename T>
BinNodePosi<T> AVL<T>::insert( const T & e ) {
	BinNodePosi<T> & x = search( e ); 
	if ( x ) return x; //若目标尚不存在
	
	BinNodePosi<T> xx = x = new BinNode<T>( e, _hot ); _size++; //则创建新节点
	// 此时，若x的父亲_hot增高，则祖父有可能失衡
	for ( BinNodePosi<T> g = _hot; g; g = g->parent ) //从_hot起，逐层检查各代祖先g
	if ( ! AvlBalanced( *g ) ) { //一旦发现g失衡，则通过调整恢复平衡
		FromParentTo(*g) = rotateAt( tallerChild( tallerChild( g ) ) );
		break; //局部子树复衡后，高度必然复原；其祖先亦必如此，故调整结束
	} else //否则（g仍平衡）
	updateHeight( g ); //只需更新其高度（注意：即便g未失衡，高度亦可能增加）
	return xx; //返回新节点位置
}
```

- AVL 树插入操作时，即使未失衡，高度也有可能增加（上图 0 处子树高度多 1 的情况，再在 其它子树插入时会造成高度增加）

## 删除
delete (x)操作可能造成至多一个失衡节点——失衡节点若出现，首个就是 x 的父亲_hot。要复平衡有两种情况：

1. 单旋：x 在失衡局部的两侧
	- ![[60B-Balanced-BST-avl-del-rebalance1.png]]
	- 删除节点 x 存在于黄色节点任意一处，红色方块可有可无；
	- 复衡后子树的高度未必复原，更高的祖先可能随之失衡；
	- 失衡会沿着祖先向上传播，因此最多作 O (logn)次调整；

2. 双旋：x 在失衡局部的中间
	- ![[60B-Balanced-BST-avl-del-rebalance2.png]]
	- 哪里出现失衡，就对其失衡的子树进行旋转，在对自身旋转。
	- 同样失衡会继续向上传播，最多要作 O (logn)次调整。
	- 经 Knuth 测试、统计指出，平均而言仅需 0.21 次旋转即可恢复平衡。

```
template <typename T>
bool AVL<T>::remove( const T & e ) {
	BinNodePosi<T> & x = search( e );
	if ( !x ) return false; //若目标的确存在

	removeAt( x, _hot ); _size--; //则在按BST规则删除之后，_hot及祖先均有可能失衡
	// 以下，从_hot出发逐层向上，依次检查各代祖先g
	for ( BinNodePosi<T> g = _hot; g; g = g->parent ) {
		if ( ! AvlBalanced( *g ) ) //一旦发现g失衡，则通过调整恢复平衡
			g = FromParentTo( *g ) = rotateAt( tallerChild( tallerChild( g ) ) );
		updateHeight( g ); //更新高度（注意：即便g未曾失衡或已恢复平衡，高度均可能降低）
	} //可能需做过Ω(logn)次调整；无论是否做过调整，全树高度均可能下降
	return true; //删除成功
}

```

- 即便删除未导致失衡，AVL 树的高度也可能降低；

## 3+4 重构
旋转所涉及的局部操作可以通过替换失衡节点的子树来完成，称为 3+4 重构（3 个节点，4 个子树）：
![[60B-Balanced-BST-avl-3+4-reconstruction.png]]
- 设 g 为最低失衡节点，考查其祖孙三代：g~p~v，按中序遍历次序分别命名 g 及其左右孩子为 b、a、c；
- 对 g 的孙节点及子树（无论是否为空），重命名为 T0 < T1 < T2 < T3；
- 旋转的本质就是在这个局部进行等价变换，并且要求保证中序遍历的次序不变：T0 < a < T1 < b < T2 < c < T3
```
/**
 * 按照“3 + 4”结构联接3个节点及其四棵子树，返回重组之后的局部子树根节点位置（即b）
 * 子树根节点与上层节点之间的双向联接，均须由上层调用者完成
 * 可用于AVL和RedBlack的局部平衡调整
 **/
template <typename T> BinNodePosi<T> BST<T>::connect34 (
   BinNodePosi<T> a, BinNodePosi<T> b, BinNodePosi<T> c,
   BinNodePosi<T> T0, BinNodePosi<T> T1, BinNodePosi<T> T2, BinNodePosi<T> T3
) {
   /*DSA*///print(a); print(b); print(c); printf("\n");
   a->lc = T0; if ( T0 ) T0->parent = a;
   a->rc = T1; if ( T1 ) T1->parent = a; updateHeight ( a );
   c->lc = T2; if ( T2 ) T2->parent = c;
   c->rc = T3; if ( T3 ) T3->parent = c; updateHeight ( c );
   b->lc = a; a->parent = b;
   b->rc = c; c->parent = b; updateHeight ( b );
   return b; //该子树新的根节点
}
```

之后来实现旋转操作：
```
/**
 * BST节点旋转变换统一算法（3节点 + 4子树），返回调整之后局部子树根节点的位置
 * 注意：尽管子树根会正确指向上层节点（如果存在），但反向的联接须由上层函数完成
 **/
template <typename T> BinNodePosi<T> BST<T>::rotateAt( BinNodePosi<T> v ) { //v为非空孙辈节点
   
   BinNodePosi<T> p = v->parent; 
   BinNodePosi<T> g = p->parent;
	
   //视v、p和g相对位置分四种情况
   if ( IsLChild( *p ) ) // zig
      if ( IsLChild( *v ) ) { /* zig-zig */
         p->parent = g->parent; //向上联接
         return connect34( v, p, g, v->lc, v->rc, p->rc, g->rc );
      } else { /* zig-zag */ 
         v->parent = g->parent; //向上联接
         return connect34( p, v, g, p->lc, v->lc, v->rc, g->rc );
      }
   else // zag
      if ( IsRChild( *v ) ) { /* zag-zag */ 
         p->parent = g->parent; //向上联接
         return connect34( g, p, v, g->lc, p->lc, v->lc, v->rc );
      } else { /* zag-zig */ 
         v->parent = g->parent; //向上联接
         return connect34( g, v, p, g->lc, v->lc, v->rc, p->rc );
      }
}
```
- zig-zig:
	- ![[60B-Balanced-BST-avl-rebalance-zig-zig.png]]
- zig-zag:
	- ![[60B-Balanced-BST-avl-rebalance-zig-zag.png]]
- zag-zag:
	- ![[60B-Balanced-BST-avl-rebalance-zag-zag.png]]
- zag-zig:
	- ![[60B-Balanced-BST-avl-rebalance-zag-zig.png]]

## 性能分析
- 优点：无论查找、插入、删除，最坏情况的时间复杂度均为 O (logn)，空间复杂度 O(n)
- 缺点：
	- 平衡因子需要改造元素的数据域，或者额外封装；
	- 插入、删除的旋转消耗的时间成本还是很高，并且删除操作最多作Ω(logn)次
	- 对频繁的插入、删除操作支持不好；
	- 单词调整可能导致全树拓扑变化量为Ω(logn)；