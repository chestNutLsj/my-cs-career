
## 知识点一览

### 算法问题:

1. 总和最大区段问题
2. 动态规划求 Fibonacci 序列  
3. 最长公共子序列 LCS 问题
4. 向量元素就地循环位移问题
5. 快速幂计算问题
6. (伪)随机数生成问题
7. 快速计算整数二进制表示的 1 位数
8. 更相减损术
9. Hanoi 问题
10. 八皇后问题
11. 迷宫寻径问题
12. 如何实现列表/向量逆置?
13. 进制转换
14. 括号匹配  
15. 中缀表达式求值
16. 中缀表达式转后缀表达式
17. 直方图最大矩形
18. Steap&Queap: 维护栈和队列的 getMax ()接口
19. Huffman 树的构造策略
20. 圆桌骑士问题
21. 旅行骑士问题
22. 最低公共祖先 LCA 问题
23. 最大缝隙 MaxGap 问题 (桶排序思路)
24. 对数密度的整数集排序 (基数排序思想)
25. BFS 如何在 O (n+e)内找到直径?
26. 旅行商问题: 找出哈密尔顿环路

### 知识点细节:

1. fib (k)二分递归实例出现次数问题
2. O (√n)的形式  
3. 递归调用: 线性递归实例分析
4. 递归调用: 二分递归实例分析
5. 平均分析 Vs. 分摊分析
6. 二分查找 B 和 C 的特点
7. 二路归并排序的复杂度、优缺点与改进
8. 二分搜索的平均查找长度
9. 二分查找 A 与 Fib 查找对比
10. 插值查找: 查找区间宽度缩减速度和整体复杂度
11. 大致有序情况时归并排序的改进策略
12. 基数排序的思想基础
13. 选择排序的稳定性如何保证?
14. 插入排序的性能、交换次数分析
15. 循环节: 选择排序过程中出现无需交换的情形可能有多少次?
16. 逆序对与冒泡排序、插入排序  
17. 无序列表唯一化的最好情况是什么? 
18. 插入排序的比较次数
19. 循环节: 选择排序中无需交换的元素的数量
20. mergeSort 不平衡划分的影响: O (n^2)
21. 栈混洗: 总数、禁形判断、与括号匹配的关系
22. Catalan 数的实例
23. 中缀表达式求值实例
24. 中缀表达式转后缀表达式
25. 双栈当队: 实现与性能分析
26. 循环向量实现队列
27. 树的深度与高度关系
28. 二叉树度数、节点数、边数的关系
29. 长子兄弟表示法: 找到根的复杂度、遍历复杂度
30. 满二叉树与真二叉树的区别  
31. 完全二叉树的定义与特点
32. 先序遍历迭代版
33. 中序遍历迭代版
34. 后序遍历特点、迭代版  
35. 后序遍历应用: 表达式树
36. 二叉树重构的结论
37. 为什么先序+后序不能唯一确定二叉树?
38. 为什么真二叉树能够使先序+后序唯一确定二叉树?
39. PFC 解码的流程, 且是在线算法吗?
40. 最优编码树是真完全树?
41. 考虑权重, 真完全树还是最优编码树吗? 
42. Huffman 树的特性: 双子性、层次性
43. Huffman 树构造的改进方案: 优先级队列改进方案的时间复杂度 
44. 树的半径与 BFS
45. BST 插入的节点一定是叶节点吗?
46. BST 删除操作的两种情况
47. BST 随机生成和随机组成的区别  
48. AVL-tree 的高度与节点数关系
49. Fib-AVL 树的形态
50. AVL-tree 树高增长的可能性: 一定要失衡吗?
51. AVL-tree 树高降低的可能性: 一定要失衡吗?
52. Splay-tree 双层伸展的细节、单层伸展最多发生多少次?
53. Splay-tree 的插入: 在根部直接插入节点, 省去第二次伸展
54. Splay-tree 删除: 先伸展再根部删除之
55. Splay-tree 的势能分析
56. BTree 定义的细节: 深度、高度
57. BTree 树高、胖瘦问题
58. BTree 插入引起的上溢修复、树高增加的可能性
59. BTree 删除引起的下溢修复、下溢到根的可能
60. BST 结构与并发性 (访问延迟)的关系
61. 红黑树的高度、黑高度、黑深度、红高度
62. 红黑树双红修正: 黑高度增加的可能性
63. 红黑树的删除操作如何引起双黑冲突
64. 红黑树双黑修除: 黑高度降低的可能性
65. 红黑树插入、删除操作的总结
66. 除余法中素数的作用、算法的缺陷
67. MAD法中M、A这两个操作对除余法进行了什么改进?
68. 开放散列与封闭散列的区别是什么?
69. 开放散列的三种思路:多槽位、公共溢出区、独立链的特点
70. 开放散列和开放定址的区别?
71. 线性试探:思路、优劣、懒删除机制
72. 重散列与双散列的区别:rehash, bi-hash
73. 平方试探中表长是素数、合数有什么影响?装填因子多少时可以保证找到?
74. 双向平方试探什么情况下可以填满整个表?
75. 基数排序的时间复杂度分析
76. 基数排序如何确保正确性?底层排序一定要稳定吗?
77. 计数排序的应用场景、思路与实例
78. 计数排序每轮迭代的最后一步——扫描数据能否改为自前向后?
79. 跳转表的空间性能:塔高期望
80. 跳转表查找: 纵向跳转次数和横向跳转次数
81. 除余法导致的关键码冲突数、散列表利用率
82. 路径、环路:欧拉环路、哈密尔顿环路等概念
83. 邻接矩阵:特点、空间复杂度和利用率、性能、缺点
84. 邻接表:特点、空间复杂度、时间复杂度(琐碎)
85. 邻接矩阵和邻接表的取舍原则
86. BFS过程中顶点状态、边记号的含义
87. bfs时间复杂度
88. 图的最短路径思路、BFS过程中队列变化细节
89. 图的偏心率、半径、直径、中心
90. DFS顶点状态dTime、fTime细节、其他状态的含义
91. DFS过程中边的状态细节
92. 括号引理与活跃期
93. BFS/DFS各自适用的场景
94. 如何判断图中出现环路?
95. 如何判断是否存在欧拉环路?
96. DAG与拓扑排序的偏序、全序关系
97. 零出度拓扑排序的应用
98. 关节点、双连通图、双连通分量的定义、特点
99. DFC判断双连通图:内部节点何时为关节点?hca如何判断双连通分量?
100. 优先级搜索的时间复杂度
101. 最短路径树的概念与Dijkstra算法的局限性
102. Dijkstra算法的实例细节
103. MST的蛮力算法:Cayley公式
104. 极短跨边理论:排除环路最长边、容纳割的最短边的思路
105. Kruskal算法的思路步骤、正确性、并查集实现
106. 有向图、无向图的关联矩阵与邻接矩阵的关系
107. Prim 算法通过扰动消除由重复边引起的歧义
108. 合成数法消除 Prim 和 Dijkstra 算法的歧义性
109. 考查负权边和负权环对 Prim 和 Dijkstra 算法的影响
110. 证明 Prim 算法在多边等权时亦然成立
111. 最坏情况的 Kruskal 算法
112. 最短路径树唯一确定吗?
113. 完全二叉堆的秩的关系:内部节点最大秩,父亲的秩,左右子的秩
114. 二叉堆插入:逐层上滤步骤及效率
115. 二叉堆删除:逐层下滤步骤及效率,注意ProperParent宏
116. Floyd建堆法详细步骤、效率、具体比较次数、不适用场景
117. 大顶堆delMin操作可以达到O(logn)吗?具体是多少?
118. 胜者树细节:重赛次数、性能、用以k选取的比较次数
119. 败者树细节:思路、亚军、比较次数
120. 二叉堆对图PFS的改进:优劣
121. 多叉堆秩的计算方式
122. 多叉堆特点:堆高、下滤性能、上滤性能
123. 多叉堆实现 PFS 的优势
124. 左式堆的形态、实现
125. npl的计算方式
126. 左式堆的左倾性如何控制?左倾性的细节性质
127. 左式堆节点数与高度关系:右侧链与长度、最浅外部节点
128. 左式堆如何实现堆的合并
129. 左式堆合并的复杂度
130. 左式堆如何实现插入与删除
131. 二叉堆中如何在O(1)时间确定任何祖先的秩
132. 二叉堆中如何在 O(1)时间确定任何祖先的秩
133. 理解 next[] 表
134. 构造 next[] 表
135. KMP算法分摊分析复杂度
136. 改进 next[] 表
137. KMP算法的适用情景
138. 坏字符移动的思路
139. 构建 bc[] 表、及时间复杂度
140. 坏字符策略的性能分析与适用场景
141. 好后缀的思路
142. 借助最大匹配后缀串构造 gs[] 表
143. 构造 ss[] 表的时间复杂度
144. KMP、BM-BC、BM-GS策略性能分析与比较
145. 散列如何优化KR法?
146. 快速指纹计算:方法、复杂度
147. 轴点划分LUG版
148. 轴点划分DUP版:应对情形、改进之处、特点
149. 轴点划分LGU版:如何操作?
150. 快速排序递归深度的分析
151. 快速排序比较次数的分析:递推分析、后向分析
152. 众数与中位数:关系、确定众数候选的方法
153. 中位数选取:减治策略及复杂度
154. k选取:基于快速化分的k选取策略、复杂度
155. k选取:线性选取策略、复杂度
156. 输入敏感性与希尔排序性能分析
157. shell序列最后一趟复杂度为Ω(n^2)的原因
158. 互素元素线性组合的最大不可表示数
159. 互素步长的排序后逆序对的间距
160. (g, h)-sorted 时 d-sorting 的排序成本
161. PS步长序列的时间复杂度
162. Pratt步长序列的时间复杂度
163. Sedgewick 步长序列的时间复杂度
164. 如何基于中位数选取算法实现 k 选取？
165. Pratt 序列的详细分析

### 拓展延伸:

1. Stirling 渐近
2. Little-o-notation
3. 线性规约 Linear reduction  
4. CBA 排序算法下界分析 (比较树)
5. 动态规划
6. Fisher 随机数生成算法与分析
7. 无序向量唯一化的时间下界  
8. 指数查找
9. 马鞍查找
10. 鸡蛋查找: 分支深入代价严重不对称  
11. Bitmap
12. In-place mergesort
13. N-way merge
14. 自调整列表
15. 孪生栈
16. 费马-拉格朗日分解
17. Catalan 数实例 (14 个)
18. 键树 Trie
19. 如何证明两棵等价 BBST 在 O (logn)时间内不能完成彼此转换
20. 何种插入关键码的次序使 BTree 高度最小
21. B* 树及其优点  
22. B+树
23. Height-BBST vs. Weight-BBST
24. 斐波那契序列与最差 AVL 树的证明
25. 二分图
26. Floyd-Warshall 算法求 SPT
27. 欧式最小支撑树
28. 并查集
29. 强连通分量的求解方法
30. Fibonacci-heap
31. Min-max heap
32. Skew heap

### 习题解析好题:

1. 分摊分析实例: 二进制整数递增
2. 比较树高度分析: CBA 排序  
3. 插入多个元素到有序序列的比较次数
4. 找出向量中最大者、次大者的策略优化
5. 插入排序性能分析
6. 逆序对与插入排序分析
7. 循环节：选择排序中无需交换的元素的数量
8. mergeSort 不平衡划分的影响: O(n^2)
9. 栈混洗与禁形
10. 中缀表达式运算中根据括号数求栈规模
11. 二叉树高度与节点数问题: 引伸到真二叉树
12. PFC 编码一定解码成功吗?
13. 易错：迭代式树遍历算法的栈的最小容量  
14. 分析 travIn_I3 的时间复杂度 (succ 接口对复杂度的影响)
15. 层次遍历辅助队列容量问题
16. 层次遍历过程中辅助队列规模单峰对称的性质
17. 如何在判断任一对节点间存在“祖先-后代”关系？
18. O (n)时间内判断是否树中所有节点的数值均不小于其真祖先的数值总和
19. O (n)时间内将每个节点的数值替换为后代中最大数值
20. 如何在关键码可重复的 BST 中找到目标项的全部？
21. 2012 个内部节点的红黑树 (黑)高度的极值问题  
22. 证明: 分摊意义上红黑树重平衡中需要重染色的节点数为 O (1)
23. 表长为什么不能取作 2^k?
24. 单向平方试探法在素数长度的哈希表中的查找链分析
25. 散列表长为合数时的平方试探
26. 双向平方试探的表长分析:4k+3
27. 计数排序
28. 单向平方试探为什么会陷入死循环
29. 平面图的边数量为 O (n)的证明
30. BFS 辅助队列的特点
31. 通过拓扑排序检查是否是 DAG
32. 以 PFS 设计 prioUpdater 实现 BFS 和 DFS
33. 边权重复时极小支撑树问题
34. 如何将上滤算法的比较次数优化到 O (loglogn)
35. 估算 percolateUp 的实际期望
36. CBA 式算法构建 Huffman 树的时间下界
37. 利用频率已有序的性质在 O (n)内完成构建 Huffman 树
38. 利用多叉堆改进 Prim 算法
39. 为栈维护 getMax 接口
40. 为队列维护 getMax 接口
41. 合并大节点 AVL 树和小节点 AVL树
42. 分裂大节点 AVL 树和小节点 AVL 树
43. KMP 复杂度的严谨分析
44. ss[]和 gs[]表的构造算法复杂度
45. 模式枚举问题：匹配间隔的设置对复杂度的影响
46. 模式匹配蛮力算法的实际效率
47. LGU 策略能否应对大量元素重复的情况?  
48. 三点取中法的失衡概率计算
49. 众数定义修改为“不少于一半”的分析
50. 不等长向量长度的 `median()` 算法分析
51. 基数排序推广: 希尔排序的基本思想
52. 证明 g-sorted 的向量经过 h-sorting 后亦然保持 g-sorted

## 推荐资源

- 推荐资料：[What is an Algorithm?](https://www.programiz.com/dsa/algorithm)
- [Python Tutor code visualizer: Visualize code in Python, JavaScript, C, C++, and Java](https://pythontutor.com/render.html#)
- [visualising data structures and algorithms through animation - VisuAlgo](https://visualgo.net/)
- [Algorithm Visualizer](https://algorithm-visualizer.org/)
- [DSACPP](https://dsa.cs.tsinghua.edu.cn/~deng/ds/dsacpp/index.htm)
- 算法导论习题答案：[CLRS Solutions](https://sites.math.rutgers.edu/~ajl213/CLRS/CLRS.html)
- OIWiki：[OI Wiki - OI Wiki](https://oi-wiki.org/)