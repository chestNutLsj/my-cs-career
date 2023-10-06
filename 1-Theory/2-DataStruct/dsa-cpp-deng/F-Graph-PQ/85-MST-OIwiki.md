## 定义

在阅读下列内容之前，请务必阅读 [图论相关概念](https://oi-wiki.org/graph/concept/) 与 [树基础](https://oi-wiki.org/graph/tree-basic/) 部分，并了解以下定义：

1.  生成子图
2.  生成树

我们定义无向连通图的 **最小生成树**（Minimum Spanning Tree，MST）为边权和最小的生成树。

注意：只有连通图才有生成树，而对于非连通图，只存在生成森林。

Kruskal 算法[](#kruskal-算法 "Permanent link")
------------------------------------------

Kruskal 算法是一种常见并且好写的最小生成树算法，由 Kruskal 发明。该算法的基本思想是从小到大加入边，是个贪心算法。

### 前置知识[](#前置知识 "Permanent link")

[并查集](https://oi-wiki.org/ds/dsu/)、[贪心](https://oi-wiki.org/basic/greedy/)、[图的存储](https://oi-wiki.org/graph/save/)。

### 实现[](#实现 "Permanent link")

图示：

![](https://oi-wiki.org/graph/images/mst-2.apng)

伪代码：

![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

算法虽简单，但需要相应的数据结构来支持…… 具体来说，维护一个森林，查询两个结点是否在同一棵树中，连接两棵树。

抽象一点地说，维护一堆 **集合**，查询两个元素是否属于同一集合，合并两个集合。

其中，查询两点是否连通和连接两点可以使用并查集维护。

如果使用 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的排序算法，并且使用 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 或 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的并查集，就可以得到时间复杂度为 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的 Kruskal 算法。

### 证明[](#证明 "Permanent link")

思路很简单，为了造出一棵最小生成树，我们从最小边权的边开始，按边权从小到大依次加入，如果某次加边产生了环，就扔掉这条边，直到加入了 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 条边，即形成了一棵树。

证明：使用归纳法，证明任何时候 K 算法选择的边集都被某棵 MST 所包含。

基础：对于算法刚开始时，显然成立（最小生成树存在）。

归纳：假设某时刻成立，当前边集为 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，令 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 为这棵 MST，考虑下一条加入的边 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

如果 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 属于 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，那么成立。

否则，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 一定存在一个环，考虑这个环上不属于 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的另一条边 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)（一定只有一条）。

首先，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的权值一定不会比 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 小，不然 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 会在 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 之前被选取。

然后，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的权值一定不会比 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 大，不然 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 就是一棵比 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 还优的生成树了。

所以，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 包含了 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，并且也是一棵最小生成树，归纳成立。

Prim 算法[](#prim-算法 "Permanent link")
------------------------------------

Prim 算法是另一种常见并且好写的最小生成树算法。该算法的基本思想是从一个结点开始，不断加点（而不是 Kruskal 算法的加边）。

### 实现[](#实现_1 "Permanent link")

图示：

![](https://oi-wiki.org/graph/images/mst-3.apng)

具体来说，每次要选择距离最小的一个结点，以及用新的边更新其他结点的距离。

其实跟 Dijkstra 算法一样，每次找到距离最小的一个点，可以暴力找也可以用堆维护。

堆优化的方式类似 Dijkstra 的堆优化，但如果使用二叉堆等不支持 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) decrease-key 的堆，复杂度就不优于 Kruskal，常数也比 Kruskal 大。所以，一般情况下都使用 Kruskal 算法，在稠密图尤其是完全图上，暴力 Prim 的复杂度比 Kruskal 优，但 **不一定** 实际跑得更快。

暴力：![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

二叉堆：![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

Fib 堆：![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

伪代码：

![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

注意：上述代码只是求出了最小生成树的权值，如果要输出方案还需要记录每个点的 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 代表的是哪条边。

### 证明[](#证明_1 "Permanent link")

从任意一个结点开始，将结点分成两类：已加入的，未加入的。

每次从未加入的结点中，找一个与已加入的结点之间边权最小值最小的结点。

然后将这个结点加入，并连上那条边权最小的边。

重复 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 次即可。

证明：还是说明在每一步，都存在一棵最小生成树包含已选边集。

基础：只有一个结点的时候，显然成立。

归纳：如果某一步成立，当前边集为 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，属于 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 这棵 MST，接下来要加入边 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

如果 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 属于 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，那么成立。

否则考虑 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 中环上另一条可以加入当前边集的边 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

首先，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的权值一定不小于 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的权值，否则就会选择 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 而不是 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 了。

然后，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的权值一定不大于 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的权值，否则 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 就是一棵更小的生成树了。

因此，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 和 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的权值相等，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 也是一棵最小生成树，且包含了 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

Boruvka 算法[](#boruvka-算法 "Permanent link")
------------------------------------------

接下来介绍另一种求解最小生成树的算法——Boruvka 算法。该算法的思想是前两种算法的结合。它可以用于求解 **边权互不相同** 的无向图的最小生成森林。（无向连通图就是最小生成树。）

为了描述该算法，我们需要引入一些定义：

1.  定义 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 为我们当前找到的最小生成森林的边。在算法执行过程中，我们逐步向 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 加边，定义 **连通块** 表示一个点集 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，且这个点集中的任意两个点 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 在 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 中的边构成的子图上是连通的（互相可达）。
2.  定义一个连通块的 **最小边** 为它连向其它连通块的边中权值最小的那一条。

初始时，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，每个点各自是一个连通块：

1.  计算每个点分别属于哪个连通块。将每个连通块都设为「没有最小边」。
2.  遍历每条边 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，如果 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 和 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 不在同一个连通块，就用这条边的边权分别更新 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 和 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 所在连通块的最小边。
3.  如果所有连通块都没有最小边，退出程序，此时的 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 就是原图最小生成森林的边集。否则，将每个有最小边的连通块的最小边加入 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，返回第一步。

下面通过一张动态图来举一个例子（图源自 [维基百科](https://en.wikipedia.org/wiki/Bor%C5%AFvka%27s_algorithm)）：

![](https://oi-wiki.org/graph/images/mst-1.apng)

当原图连通时，每次迭代连通块数量至少减半，算法只会迭代不超过 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 次，而原图不连通时相当于多个子问题，因此算法复杂度是 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的。给出算法的伪代码：（修改自 [维基百科](https://en.wikipedia.org/wiki/Bor%C5%AFvka%27s_algorithm)）

![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

习题[](#习题 "Permanent link")
--------------------------

*   [「HAOI2006」聪明的猴子](https://www.luogu.com.cn/problem/P2504)
*   [「SCOI2005」繁忙的都市](https://loj.ac/problem/2149)

最小生成树的唯一性[](#最小生成树的唯一性 "Permanent link")
----------------------------------------

考虑最小生成树的唯一性。如果一条边 **不在最小生成树的边集中**，并且可以替换与其 **权值相同、并且在最小生成树边集** 的另一条边。那么，这个最小生成树就是不唯一的。

对于 Kruskal 算法，只要计算为当前权值的边可以放几条，实际放了几条，如果这两个值不一样，那么就说明这几条边与之前的边产生了一个环（这个环中至少有两条当前权值的边，否则根据并查集，这条边是不能放的），即最小生成树不唯一。

寻找权值与当前边相同的边，我们只需要记录头尾指针，用单调队列即可在 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)（m 为边数）的时间复杂度里优秀解决这个问题（基本与原算法时间相同）。

例题：[POJ 1679](http://poj.org/problem?id=1679)

次小生成树[](#次小生成树 "Permanent link")
--------------------------------

### 非严格次小生成树[](#非严格次小生成树 "Permanent link")

#### 定义[](#定义_1 "Permanent link")

在无向图中，边权和最小的满足边权和 **大于等于** 最小生成树边权和的生成树

#### 求解方法[](#求解方法 "Permanent link")

如何求 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 路径上的边权最大值呢？

我们可以使用倍增来维护，预处理出每个节点的 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 级祖先及到达其 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 级祖先路径上最大的边权，这样在倍增求 LCA 的过程中可以直接求得。

### 严格次小生成树[](#严格次小生成树 "Permanent link")

#### 定义[](#定义_2 "Permanent link")

在无向图中，边权和最小的满足边权和 **严格大于** 最小生成树边权和的生成树

#### 求解方法[](#求解方法_1 "Permanent link")

考虑刚才的非严格次小生成树求解过程，为什么求得的解是非严格的？

因为最小生成树保证生成树中 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 到 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 路径上的边权最大值一定 **不大于** 其他从 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 到 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 路径的边权最大值。换言之，当我们用于替换的边的权值与原生成树中被替换边的权值相等时，得到的次小生成树是非严格的。

解决的办法很自然：我们维护到 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 级祖先路径上的最大边权的同时维护 **严格次大边权**，当用于替换的边的权值与原生成树中路径最大边权相等时，我们用严格次大值来替换即可。

这个过程可以用倍增求解，复杂度 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

#### 代码[](#代码 "Permanent link")

```
#include <algorithm>
#include <iostream>

const int INF = 0x3fffffff;
const long long INF64 = 0x3fffffffffffffffLL;

struct Edge {
  int u, v, val;

  bool operator<(const Edge &other) const { return val < other.val; }
};

Edge e[300010];
bool used[300010];

int n, m;
long long sum;

class Tr {
 private:
  struct Edge {
    int to, nxt, val;
  } e[600010];

  int cnt, head[100010];

  int pnt[100010][22];
  int dpth[100010];
  // 到祖先的路径上边权最大的边
  int maxx[100010][22];
  // 到祖先的路径上边权次大的边，若不存在则为 -INF
  int minn[100010][22];

 public:
  void addedge(int u, int v, int val) {
    e[++cnt] = (Edge){v, head[u], val};
    head[u] = cnt;
  }

  void insedge(int u, int v, int val) {
    addedge(u, v, val);
    addedge(v, u, val);
  }

  void dfs(int now, int fa) {
    dpth[now] = dpth[fa] + 1;
    pnt[now][0] = fa;
    minn[now][0] = -INF;
    for (int i = 1; (1 << i) <= dpth[now]; i++) {
      pnt[now][i] = pnt[pnt[now][i - 1]][i - 1];
      int kk[4] = {maxx[now][i - 1], maxx[pnt[now][i - 1]][i - 1],
                   minn[now][i - 1], minn[pnt[now][i - 1]][i - 1]};
      // 从四个值中取得最大值
      std::sort(kk, kk + 4);
      maxx[now][i] = kk[3];
      // 取得严格次大值
      int ptr = 2;
      while (ptr >= 0 && kk[ptr] == kk[3]) ptr--;
      minn[now][i] = (ptr == -1 ? -INF : kk[ptr]);
    }

    for (int i = head[now]; i; i = e[i].nxt) {
      if (e[i].to != fa) {
        maxx[e[i].to][0] = e[i].val;
        dfs(e[i].to, now);
      }
    }
  }

  int lca(int a, int b) {
    if (dpth[a] < dpth[b]) std::swap(a, b);

    for (int i = 21; i >= 0; i--)
      if (dpth[pnt[a][i]] >= dpth[b]) a = pnt[a][i];

    if (a == b) return a;

    for (int i = 21; i >= 0; i--) {
      if (pnt[a][i] != pnt[b][i]) {
        a = pnt[a][i];
        b = pnt[b][i];
      }
    }
    return pnt[a][0];
  }

  int query(int a, int b, int val) {
    int res = -INF;
    for (int i = 21; i >= 0; i--) {
      if (dpth[pnt[a][i]] >= dpth[b]) {
        if (val != maxx[a][i])
          res = std::max(res, maxx[a][i]);
        else
          res = std::max(res, minn[a][i]);
        a = pnt[a][i];
      }
    }
    return res;
  }
} tr;

int fa[100010];

int find(int x) { return fa[x] == x ? x : fa[x] = find(fa[x]); }

void Kruskal() {
  int tot = 0;
  std::sort(e + 1, e + m + 1);
  for (int i = 1; i <= n; i++) fa[i] = i;

  for (int i = 1; i <= m; i++) {
    int a = find(e[i].u);
    int b = find(e[i].v);
    if (a != b) {
      fa[a] = b;
      tot++;
      tr.insedge(e[i].u, e[i].v, e[i].val);
      sum += e[i].val;
      used[i] = 1;
    }
    if (tot == n - 1) break;
  }
}

int main() {
  std::ios::sync_with_stdio(0);
  std::cin.tie(0);
  std::cout.tie(0);

  std::cin >> n >> m;
  for (int i = 1; i <= m; i++) {
    int u, v, val;
    std::cin >> u >> v >> val;
    e[i] = (Edge){u, v, val};
  }

  Kruskal();
  long long ans = INF64;
  tr.dfs(1, 0);

  for (int i = 1; i <= m; i++) {
    if (!used[i]) {
      int _lca = tr.lca(e[i].u, e[i].v);
      // 找到路径上不等于 e[i].val 的最大边权
      long long tmpa = tr.query(e[i].u, _lca, e[i].val);
      long long tmpb = tr.query(e[i].v, _lca, e[i].val);
      // 这样的边可能不存在，只在这样的边存在时更新答案
      if (std::max(tmpa, tmpb) > -INF)
        ans = std::min(ans, sum - std::max(tmpa, tmpb) + e[i].val);
    }
  }
  // 次小生成树不存在时输出 -1
  std::cout << (ans == INF64 ? -1 : ans) << '\n';
  return 0;
}
```

瓶颈生成树[](#瓶颈生成树 "Permanent link")
--------------------------------

### 定义[](#定义_3 "Permanent link")

无向图 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的瓶颈生成树是这样的一个生成树，它的最大的边权值在 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的所有生成树中最小。

### 性质[](#性质 "Permanent link")

**最小生成树是瓶颈生成树的充分不必要条件。** 即最小生成树一定是瓶颈生成树，而瓶颈生成树不一定是最小生成树。

关于最小生成树一定是瓶颈生成树这一命题，可以运用反证法证明：我们设最小生成树中的最大边权为 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，如果最小生成树不是瓶颈生成树的话，则瓶颈生成树的所有边权都小于 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)，我们只需删去原最小生成树中的最长边，用瓶颈生成树中的一条边来连接删去边后形成的两棵树，得到的新生成树一定比原最小生成树的权值和还要小，这样就产生了矛盾。

### 例题[](#例题 "Permanent link")

POJ 2395 Out of Hay

给出 n 个农场和 m 条边，农场按 1 到 n 编号，现在有一人要从编号为 1 的农场出发到其他的农场去，求在这途中他最多需要携带的水的重量，注意他每到达一个农场，可以对水进行补给，且要使总共的路径长度最小。 题目要求的就是瓶颈树的最大边，可以通过求最小生成树来解决。

最小瓶颈路[](#最小瓶颈路 "Permanent link")
--------------------------------

### 定义[](#定义_4 "Permanent link")

无向图 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 中 x 到 y 的最小瓶颈路是这样的一类简单路径，满足这条路径上的最大的边权在所有 x 到 y 的简单路径中是最小的。

### 性质[](#性质_1 "Permanent link")

根据最小生成树定义，x 到 y 的最小瓶颈路上的最大边权等于最小生成树上 x 到 y 路径上的最大边权。虽然最小生成树不唯一，但是每种最小生成树 x 到 y 路径的最大边权相同且为最小值。也就是说，每种最小生成树上的 x 到 y 的路径均为最小瓶颈路。

但是，并不是所有最小瓶颈路都存在一棵最小生成树满足其为树上 x 到 y 的简单路径。

例如下图：

![](https://oi-wiki.org/graph/images/mst5.png)

1 到 4 的最小瓶颈路显然有以下两条：1-2-3-4。1-3-4。

但是，1-2 不会出现在任意一种最小生成树上。

### 应用[](#应用 "Permanent link")

由于最小瓶颈路不唯一，一般情况下会询问最小瓶颈路上的最大边权。

也就是说，我们需要求最小生成树链上的 max。

倍增、树剖都可以解决，这里不再展开。

Kruskal 重构树[](#kruskal-重构树 "Permanent link")
--------------------------------------------

### 定义[](#定义_5 "Permanent link")

在跑 Kruskal 的过程中我们会从小到大加入若干条边。现在我们仍然按照这个顺序。

首先新建 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 个集合，每个集合恰有一个节点，点权为 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

每一次加边会合并两个集合，我们可以新建一个点，点权为加入边的边权，同时将两个集合的根节点分别设为新建点的左儿子和右儿子。然后我们将两个集合和新建点合并成一个集合。将新建点设为根。

不难发现，在进行 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 轮之后我们得到了一棵恰有 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 个叶子的二叉树，同时每个非叶子节点恰好有两个儿子。这棵树就叫 Kruskal 重构树。

举个例子：

![](https://oi-wiki.org/graph/images/mst5.png)

这张图的 Kruskal 重构树如下：

![](https://oi-wiki.org/graph/images/mst6.png)

### 性质[](#性质_2 "Permanent link")

不难发现，原图中两个点之间的所有简单路径上最大边权的最小值 = 最小生成树上两个点之间的简单路径上的最大值 = Kruskal 重构树上两点之间的 LCA 的权值。

也就是说，到点 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的简单路径上最大边权的最小值 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的所有点 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 均在 Kruskal 重构树上的某一棵子树内，且恰好为该子树的所有叶子节点。

我们在 Kruskal 重构树上找到 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 到根的路径上权值 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的最浅的节点。显然这就是所有满足条件的节点所在的子树的根节点。

如果需要求原图中两个点之间的所有简单路径上最小边权的最大值，则在跑 Kruskal 的过程中按边权大到小的顺序加边。

[「LOJ 137」最小瓶颈路 加强版](https://loj.ac/problem/137)

[NOI 2018 归程](https://uoj.ac/problem/393)

首先预处理出来每一个点到根节点的最短路。

我们构造出来根据海拔的最大生成树。显然每次询问可以到达的节点是在最小生成树和询问点的最小边权 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的节点。

根据 Kruskal 重构树的性质，这些节点满足均在一棵子树内同时为其所有叶子节点。

也就是说，我们只需要求出 Kruskal 重构树上每一棵子树叶子的权值 min 就可以支持子树询问。

询问的根节点可以使用 Kruskal 重构树上倍增的方式求出。

时间复杂度 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)。

* * *

> 本页面最近更新：2023/3/22 15:46:23，[更新历史](https://github.com/OI-wiki/OI-wiki/commits/master/docs/graph/mst.md)  
> 发现错误？想一起完善？ [在 GitHub 上编辑此页！](https://oi-wiki.org/edit-landing/?ref=/graph/mst.md "编辑此页")  
> 本页面贡献者：[diauweb](https://github.com/diauweb), [Enter-tainer](https://github.com/Enter-tainer), [abc1763613206](https://github.com/abc1763613206), [bear-good](https://github.com/bear-good), [billchenchina](https://github.com/billchenchina), [Chrogeek](https://github.com/Chrogeek), [greyqz](https://github.com/greyqz), [HeRaNO](https://github.com/HeRaNO), [Ir1d](https://github.com/Ir1d), [Marcythm](https://github.com/Marcythm), [ouuan](https://github.com/ouuan), [partychicken](https://github.com/partychicken), [renbaoshuo](https://github.com/renbaoshuo), [ShadowsEpic](https://github.com/ShadowsEpic), [sshwy](https://github.com/sshwy), [stevebraveman](https://github.com/stevebraveman), [StudyingFather](https://github.com/StudyingFather), [Xeonacid](https://github.com/Xeonacid), [zhouyuyang2002](https://github.com/zhouyuyang2002), [ayuusweetfish](https://github.com/ayuusweetfish), [CCXXXI](https://github.com/CCXXXI), [ChungZH](https://github.com/ChungZH), [Fomalhauthmj](https://github.com/Fomalhauthmj), [Haohu Shen](mailto:haohu.shen@ucalgary.ca), [Henry-ZHR](https://github.com/Henry-ZHR), [kawa-yoiko](https://github.com/kawa-yoiko), [kenlig](https://github.com/kenlig), [ksyx](https://github.com/ksyx), [Menci](https://github.com/Menci), [nalemy](https://github.com/nalemy), [ylxmf2005](https://github.com/ylxmf2005), [YOYO-UIAT](https://github.com/YOYO-UIAT), [yzxoi](https://github.com/yzxoi)  
> 本页面的全部内容在 **[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 和 [SATA](https://github.com/zTrix/sata-license)** 协议之条款下提供，附加条款亦可能应用