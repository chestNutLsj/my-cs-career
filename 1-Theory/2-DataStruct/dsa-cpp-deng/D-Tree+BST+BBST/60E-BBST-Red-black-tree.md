## Red-black Tree
### 动机
#### 并发性
BST 的访问需要稳定的结构——修改导致的结构变化，需要加锁，此时禁止其它进程访问该 BST，直到完成结构变化后解锁。（这一周期决定了访问 BST 的延迟）

- Splay Tree 的结构变化剧烈——最差可达 O (n)
- AVL Tree：remove 时为 O (logn)，insert 可以保证 O (1)
- Red-black Tree：无论 insert 或 remove，都可以保证 O (1)
	- [[50-CPU调度#CFS 调度|Linux进程的CFS调度策略]]

#### 持久性
若要实现 BST 对历史版本的访问，BF 方法是对每个版本独立保存、各版本自成一个搜索结构：
- 单次操作 O (logh+logn)，累计 O (n x h)的时间和空间。（此处 h 指 history 的数量）

若要实现复杂度控制在 O (n+hlogn)以内，则要利用相邻版本之间的相关性（历史版本在时间上连续，自然结构上有前后的相关性）：
![[60E-BBST-Red-black-tree-partial-persistence.png]]
- 如此，就树形结构的拓扑而言，相邻版本之间的结构差异不超过 O (1)

### 红黑树的规则
由红黑两类节点组成的 BST，统一增设外部节点 NULL 使 BST 为真二叉树。
1. 树根必为黑色；
2. 外部节点也为黑色；
3. 红节点只能有黑孩子和黑父亲——即红节点不能连续出现、作父子；
4. 外部节点的黑深度相等——其黑祖先的数目相等，亦定义为全树的黑高度，同理，子树的黑高度就是后代中 NULL 的相对黑深度：
![[60E-BBST-Red-black-tree-instance.png]]

### 红黑树与 (2,4)-BTree
将红节点抬升至与其黑父亲等高，则每棵红黑树都对应于一棵 (2,4)-BTree：
![[60E-BBST-Red-black-tree-collapse-to-btree.png]]

### 红黑树的高度
- 包含 n 个内部节点的红黑树 T，高度为 O (logn)——BTree 是平衡的，等价地 Red-black Tree 自然也平衡；
- 若红黑树 T 的高度为 h，对应红高度为 R，黑高度为 H，则有如下关系：H <= h <= R+H <= 2H；
- 将红黑树 T 收拢成一棵 B 树 $T_B$，则红黑树的黑高度是 $T_B$ 的高度，$T_B$ 的每个超级节点都恰好包含红黑树 T 的一个黑节点；
- 可以推断得，红黑树的高度 $H\le \log_{\lceil \frac{4}{2}\rceil} \frac{n+1}{2}+1=\log_{2}(n+1)$ 
![[60E-BBST-Red-black-tree-red-height-black-height.png]]

### ADT
```
template <typename T>
class RedBlack : public BST<T> { //红黑树
public: //BST::search()等其余接口可直接沿用
	BinNodePosi<T> insert( const T & e ); //插入（重写）
	bool remove( const T & e ); //删除（重写）
protected: 
	void solveDoubleRed( BinNodePosi<T> x ); //双红修正
	void solveDoubleBlack( BinNodePosi<T> x ); //双黑修正
	Rank updateHeight( BinNodePosi<T> x ); //更新节点x的高度（重写）
};

#define stature( p ) ( ( p ) ? ( p )->height : 0 ) //外部节点黑高度为0，以上递推

template <typename T> 
int RedBlack<T>::updateHeight( BinNodePosi<T> x ){
	return x->height = IsBlack( x ) + max( stature( x->lc ), stature( x->rc ) ); //只计算黑节点
}
```

### 插入
按 BST 的规则插入关键码 e (x = insert(e))，常规算法中 x 必为叶节点。
1. 不妨设 x 的父亲 p=x->parent 存在（否则 x 就是根，直接插入就好了），
2. 将 x 染红：x->color = isRoot (x)? B: R;
3. 此时，仍然满足红黑树规则的 1、2、4 规则，但可能出现双红问题：
![[60E-BBST-Red-black-tree-double-red.png]]
#### 实现插入
```
template <typename T> 
BinNodePosi<T> RedBlack<T>::insert( const T & e ) {
	// 确认目标节点不存在（留意对_hot的设置）
	BinNodePosi<T> & x = search( e ); 
	if ( x ) return x;
	
	// 创建红节点x，以_hot为父，黑高度 = 0
	x = new BinNode<T>( e, _hot, NULL, NULL, 0 ); 
	_size++;
	
	// 如有必要，需做双红修正，再返回插入的节点
	BinNodePosi<T> xOld = x; 
	solveDoubleRed( x ); return xOld;
} //无论原树中是否存有e，返回时总有x->data == e
```

#### 双红修正
![[60E-BBST-Red-black-tree-double-red.png]]
考查祖父 g=p->parent，和叔父 u=uncle (x)=sibling (p)，根据 u 的颜色分别处理：
- RR-1:  u->color == B
	- 此时，x、p、g 的四个孩子（可能包含外部节点）全为黑且黑高度相同；
	- 则进行 3+4 重构，并且将 b 转黑，a 和 c 转红，
	- 从 B 树的角度理解，双红冲突的本质就是在三叉节点中插入红关键码后黑关键码不再居中（RRB 或 BRR）——重新染色即可：
	- ![[60E-BBST-Red-black-tree-RR1-1.png]]
	- ![[60E-BBST-Red-black-tree-RR1-2.png]]
	- ![[60E-BBST-Red-black-tree-RR1-3.png]]
- RR-2:  u->color == R
	- 此时对应的 B 树中，等效于超级节点发生上溢，
	- 则将 p 与 u 转黑，g 转红，
	- 在 B 树中等效于节点分裂、g 上升一层：
	- ![[60E-BBST-Red-black-tree-RR2-1.png]]
	- ![[60E-BBST-Red-black-tree-RR2-2.png]]
	- ![[60E-BBST-Red-black-tree-RR2-3.png]]
	- 同样，类似于 B 树的分裂上溢，红黑树如此双红修正可能导致祖父 g 与曾祖父 gg 又出现双红冲突（持续上溢分裂），依法炮制即可，因此可能上升至树根，此时将树根转为黑色——整棵树黑高度+1；
	- ![[60E-BBST-Red-black-tree-RR2-spread.png]]

#### 实现双红修正
```
/**
 * RedBlack双红调整算法：解决节点x与其父均为红色的问题。分为两大类情况：
 *    RR-1：2次颜色翻转，2次黑高度更新，1~2次旋转，不再递归
 *    RR-2：3次颜色翻转，3次黑高度更新，0次旋转，需要递归
 **/
template <typename T> 
void RedBlack<T>::solveDoubleRed( BinNodePosi<T> x ) { // x当前必为红
	if ( IsRoot( *x ) ) {
	//若已（递归）转至树根，则将其转黑，整树黑高度也随之递增
	_root->color = RB_BLACK; 
	_root->height++; return;
	} //否则，x的父亲p必存在
	
	BinNodePosi<T> p = x->parent; if ( IsBlack( p ) ) return; //若p为黑，则可终止调整。否则
	BinNodePosi<T> g = p->parent; //既然p为红，则x的祖父必存在，且必为黑色
	BinNodePosi<T> u = uncle( x ); //以下，视x叔父u的颜色分别处理
	
	if ( IsBlack( u ) ) { // u为黑色（含NULL）时 
	    if ( IsLChild( *x ) == IsLChild( *p ) ) //若x与p同侧（即zIg-zIg或zAg-zAg），则
	        p->color = RB_BLACK; //p由红转黑，x保持红
	    else //若x与p异侧（即zIg-zAg或zAg-zIg），则
	        x->color = RB_BLACK; //x由红转黑，p保持红
	    g->color = RB_RED; //g必定由黑转红
		//以上虽保证总共两次染色，但因增加了判断而得不偿失
		//在旋转后将根置黑、孩子置红，虽需三次染色但效率更高
	    BinNodePosi<T> gg = g->parent; //曾祖父（great-grand parent）
	    BinNodePosi<T> r = FromParentTo( *g ) = rotateAt( x ); //调整后的子树根节点
	    r->parent = gg; //与原曾祖父联接
   } else { //若u为红色
	    p->color = RB_BLACK; p->height++; //p由红转黑
	    u->color = RB_BLACK; u->height++; //u由红转黑
	    g->color = RB_RED; //在B-树中g相当于上交给父节点的关键码，故暂标记为红
	    solveDoubleRed( g ); //继续调整：若已至树根，接下来的递归会将g转黑（尾递归，不难改为迭代）
	}
}
```

#### 双红修复复杂度分析
重构、染色只需要常数时间，故只需统计总次数：
- RedBlack:: insert ()只需要 O (logn)时间，期间至多作 O (logn)次重染色、O (1)次旋转
- 即 RR-2 至多可以出现 O (logn)次，而 RR1 至多出现 1 次，流程图如下：
- ![[60E-BBST-Red-black-tree-doubleRed-flow.png]]

| situation | uncle color      | rotate num | recolor | result                           |
| --------- | ---------------- | ---------- | ------- | -------------------------------- |
| RR-1      | U.color == Black | 1~2        | 2       | 调整随即完成                     |
| RR-2      | U.color == Red   | 0          | 3       | 可能再次导致双红，但势必规模缩小 |
### 删除
按照 BST 常规算法，执行 `r=removeAt (x,_hot)`，实际被摘除的可能是 x 的前驱或后继 w，无论如何，不妨统称为 x：
1. x 由孩子 r 接替，此时另一孩子 k 必定为 NULL；
2. 随后的调整过程中，x 可能逐层上升——故假想地、等效地理解：k 是一棵黑高度与 r 相等的子树且随 x 一并摘除（尽管实际上也未出现过）
3. 完成 removeAt 之后，条件 1、2 满足，条件 3 、4 未必满足；
	- 原树中考查 x 与 r，若 x 为红，则条件 3、4 自然满足；
	- 若 r 为红，则令其与 x 交换颜色，再用上条操作即可——删除完成！
	- 若 x 与 r 均黑，则出现双黑冲突——摘除 x 并代之以 r 后，全树黑深度不再统一，即等价于 B 树中 x 所属节点下溢——需要进行[[#双黑修复]]
![[60E-BBST-Red-black-tree-delete.png]]

![[60E-BBST-Red-black-tree-delete2.png]]
#### 实现删除
```
template <typename T> 
bool RedBlack<T>::remove( const T& e ) { //从红黑树中删除关键码e
	BinNodePosi<T>& x = search( e ); 
	if ( !x ) return false; //确认目标存在（留意_hot的设置）
	BinNodePosi<T> r = removeAt( x, _hot ); //删除_hot的某个孩子，r指向其接替者
	if ( !( --_size ) ) return true; //若删除后为空树，可直接返回

	if ( !_hot )  {
		//若刚被删除的是根节点，则将其置黑，并更新黑高度
		_root->color = RB_BLACK;
		updateHeight( _root ); 
		return true; 
	}
	// assert: 以下，原x（现r）必非根，_hot必非空
	//若所有祖先的黑深度依然平衡，则无需调整
	if ( BlackHeightUpdated( *_hot ) ) return true; 

	// 至此，必定失衡，若替代节点r为红，则只需简单地反转其颜色
	if ( IsRed( r ) ) { 
		r->color = RB_BLACK; 
		r->height++; 
		return true; 
	}
	// assert: 以下，原x（现r）均为黑色
	solveDoubleBlack( r ); 
	return true; //经双黑调整后返回
} //若目标节点存在且被删除，返回true；否则返回false
```

#### 双黑修复
![[60E-BBST-Red-black-tree-double-black.png]]
考查 r 的父亲 p 和兄弟 s，分以下四种情况处理：
1. BB-1:  s 为黑且至少有一个红孩子t
	- 通过 3+4 重构后，r 保持黑色，将 a、c 染黑，b 继承 p 的颜色，此时红黑树的性质在全局恢复，删除完成！
	- ![[60E-BBST-Red-black-tree-BB1-1.png]]
	- 等价于通过关键码的旋转，消除超级节点的下溢——对应的 B 树中，p 若为红，其超级节点的两侧必有一黑；p 若为黑，必自成超级节点：
	- ![[60E-BBST-Red-black-tree-BB1-2.png]]

2. BB-2R:  s 为黑，且两个孩子均为黑，p 为红
	- r 保持黑，s 转红，p 转黑，红黑树的性质在全局恢复；
	- 对应的 B 树中，等效于下溢节点与兄弟合并，此后==上层节点不会继续下溢==（合并之前，p 的两侧必有一个黑关键码）：
	- ![[60E-BBST-Red-black-tree-BB2R-1.png]]
	- ![[60E-BBST-Red-black-tree-BB2R-2.png]]

3. BB-2B：s 为黑，且两个孩子均为黑，p 为黑
	- s 转红，r 与 p 保持黑，红黑树的性质在局部恢复；
	- 对应的 B 树中，等效于下溢节点与兄弟合并——合并前 p 和 s 均属与单关键码节点，因此孩子的下溢修复后，父节点可能又会下溢，不过最多下溢至根 O (logn)步：
	- ![[60E-BBST-Red-black-tree-BB2B-1.png]]
	- ![[60E-BBST-Red-black-tree-BB2B-2.png]]

4. BB-3: s 为红，其余孩子皆为黑
	- 绕 p 单旋，s 红转黑，p 黑转红，虽然黑高度仍然异常，但 r 有了一个新的黑兄弟 r'，故能转化为前述情况，并且 p 已经为红，必然情况是 BB-2R 或 BB-1，这样再过一轮调整必然全局恢复红黑树的性质：
	- ![[60E-BBST-Red-black-tree-BB3-1.png]]
	- ![[60E-BBST-Red-black-tree-BB3-2.png]]

#### 实现双黑修复
```
/**
 * RedBlack双黑调整算法：解决节点x与被其替代的节点均为黑色的问题
 * 分为三大类共四种情况：
 *    BB-1 ：2次颜色翻转，2次黑高度更新，1~2次旋转，不再递归
 *    BB-2R：2次颜色翻转，2次黑高度更新，0次旋转，不再递归
 *    BB-2B：1次颜色翻转，1次黑高度更新，0次旋转，需要递归
 *    BB-3 ：2次颜色翻转，2次黑高度更新，1次旋转，转为BB-1或BB2R
 **/
template <typename T> void RedBlack<T>::solveDoubleBlack( BinNodePosi<T> r ) {
	BinNodePosi<T> p = r ? r->parent : _hot; 
	if ( !p ) return; //确定r的父亲，若父亲不存在，则被删除者x为根节点，平凡情况不必修复

	BinNodePosi<T> s = ( r == p->lc ) ? p->rc : p->lc; //确定r的兄弟
	if ( IsBlack( s ) ) { //兄弟s为黑
	    BinNodePosi<T> t = NULL; //确定s的红孩子（若左、右孩子皆红，左者优先；皆黑时为NULL），根据该结果判断情况
	    if ( IsRed( s->rc ) ) t = s->rc; //右子
	    if ( IsRed( s->lc ) ) t = s->lc; //左子
	    if ( t ) { //黑s有红孩子：BB-1
	        RBColor oldColor = p->color; //备份原子树根节点p颜色，并对t及其父亲、祖父
		    // 以下，通过旋转重平衡，并将新子树的左、右孩子染黑
	        BinNodePosi<T> b = FromParentTo( *p ) = rotateAt( t ); //旋转
	        if ( HasLChild( *b ) ) { 
		        b->lc->color = RB_BLACK;
		        updateHeight( b->lc ); 
			} //左子
	        if ( HasRChild( *b ) ) { 
		        b->rc->color = RB_BLACK; 
		        updateHeight( b->rc ); 
			} //右子
	        b->color = oldColor; 
	        updateHeight( b ); //新子树根节点继承原根节点的颜色
	    } else { //黑s无红孩子
	        s->color = RB_RED; 
	        s->height--; //s转红
	        if ( IsRed( p ) ) { // BB-2R：s孩子均黑，p红
	            p->color = RB_BLACK; //p转黑，但黑高度不变
	        } else { //BB-2B：s孩子均黑，p黑
	            p->height--; //p保持黑，但黑高度下降
	            solveDoubleBlack( p ); //递归上溯
	        }
	    }
	} else { //兄弟s为红：BB-3：s红（双子俱黑）
	    s->color = RB_BLACK; 
	    p->color = RB_RED; //s转黑，p转红
	    
	    BinNodePosi<T> t = IsLChild( *s ) ? s->lc : s->rc; //取t与其父s同侧
	    _hot = p; 
	    FromParentTo( *p ) = rotateAt( t ); //对t及其父亲、祖父做平衡调整
	    solveDoubleBlack( r ); //继续修正r处双黑――此时的p已转红，故后续只能是BB-1或BB-2R
	}
}
```

#### 双黑修复复杂度分析
- 删除只需 O (logn)时间，涉及 O (logn)次重染色，O (1)次旋转：
![[60E-BBST-Red-black-tree-double-black-flow.png]]

| situation | color                 | rotate | recolor | result                           |
| --------- | --------------------- | ------ | ------- | -------------------------------- |
| BB-1      | s=black, s->child=red | 1~2    | 3       | 调整随即完成                     |
| BB-2R     | s=black, p=red        | 0      | 2       | 调整随即完成                     |
| BB-2B     | s=black, p=black      | 0      | 1       | 必然再次双黑，但下溢情况上升一层 |
| BB-3      | s=red                 | 1      | 2       | 转为 BB-1 或 BB-2R                                 |
![[红黑树.png]]