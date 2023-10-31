## Introduction

In computer science, a **Fibonacci heap** is a data structure for priority queue operations, consisting of a collection of heap-ordered trees. It has a better amortized running time than many other priority queue data structures including the binary heap and binomial heap. Michael L. Fredman and Robert E. Tarjan developed Fibonacci heaps in 1984 and published them in a scientific journal in 1987. Fibonacci heaps are named after the Fibonacci numbers, which are used in their running time analysis.

For the Fibonacci heap, the find-minimum operation takes constant (_O_(1)) amortized time. The insert and decrease key operations also work in constant amortized time. Deleting an element (most often used in the special case of deleting the minimum element) works in _O_(log _n_) amortized time, where _n_ is the size of the heap. This means that starting from an empty data structure, any sequence of _a_ insert and decrease key operations and _b_ delete operations would take _O_(_a_ + _b_ log _n_) worst case time, where _n_ is the maximum heap size. In a binary or binomial heap, such a sequence of operations would take _O_((_a_ + _b_) log _n_) time. A Fibonacci heap is thus better than a binary or binomial heap when _b_ is smaller than _a_ by a non-constant factor. It is also possible to merge two Fibonacci heaps in constant amortized time, improving on the logarithmic merge time of a binomial heap, and improving on binary heaps which cannot handle merges efficiently.

Using Fibonacci heaps for priority queues improves the asymptotic running time of important algorithms, such as Dijkstra's algorithm for computing the shortest path between two nodes in a graph, compared to the same algorithm using other slower priority queue data structures.

Structure
---------

![](//upload.wikimedia.org/wikipedia/commons/thumb/4/45/Fibonacci_heap.png/230px-Fibonacci_heap.png)Figure 1. Example of a Fibonacci heap. It has three trees of degrees 0, 1 and 3. Three vertices are marked (shown in blue). Therefore, the potential of the heap is 9 (3 trees + 2 × (3 marked-vertices)).

A Fibonacci heap is a collection of trees satisfying the minimum-heap property, that is, the key of a child is always greater than or equal to the key of the parent. This implies that the minimum key is always at the root of one of the trees. Compared with binomial heaps, the structure of a Fibonacci heap is more flexible. The trees do not have a prescribed shape and in the extreme case the heap can have every element in a separate tree. This flexibility allows some operations to be executed in a lazy manner, postponing the work for later operations. For example, merging heaps is done simply by concatenating the two lists of trees, and operation _decrease key_ sometimes cuts a node from its parent and forms a new tree.

However, at some point order needs to be introduced to the heap to achieve the desired running time. In particular, degrees of nodes (here degree means the number of direct children) are kept quite low: every node has degree at most _log n_ and the size of a subtree rooted in a node of degree _k_ is at least _F__k_+2, where _Fk_ is the _k_th Fibonacci number. This is achieved by the rule that we can cut at most one child of each non-root node. When a second child is cut, the node itself needs to be cut from its parent and becomes the root of a new tree (see Proof of degree bounds, below). The number of trees is decreased in the operation _delete minimum_, where trees are linked together.

As a result of a relaxed structure, some operations can take a long time while others are done very quickly. For the amortized running time analysis, we use the potential method, in that we pretend that very fast operations take a little bit longer than they actually do. This additional time is then later combined and subtracted from the actual running time of slow operations. The amount of time saved for later use is measured at any given moment by a potential function. The potential of a Fibonacci heap is given by

Potential = _t_ + 2_m_

where _t_ is the number of trees in the Fibonacci heap, and _m_ is the number of marked nodes. A node is marked if at least one of its children was cut since this node was made a child of another node (all roots are unmarked). The amortized time for an operation is given by the sum of the actual time and _c_ times the difference in potential, where _c_ is a constant (chosen to match the constant factors in the _O_ notation for the actual time).

Thus, the root of each tree in a heap has one unit of time stored. This unit of time can be used later to link this tree with another tree at amortized time 0. Also, each marked node has two units of time stored. One can be used to cut the node from its parent. If this happens, the node becomes a root and the second unit of time will remain stored in it as in any other root.

Implementation of operations
----------------------------

To allow fast deletion and concatenation, the roots of all trees are linked using a circular doubly linked list. The children of each node are also linked using such a list. For each node, we maintain its number of children and whether the node is marked. Moreover, we maintain a pointer to the root containing the minimum key.

Operation **find minimum** is now trivial because we keep the pointer to the node containing it. It does not change the potential of the heap, therefore both actual and amortized cost are constant.

As mentioned above, **merge** is implemented simply by concatenating the lists of tree roots of the two heaps. This can be done in constant time and the potential does not change, leading again to constant amortized time.

Operation **insert** works by creating a new heap with one element and doing merge. This takes constant time, and the potential increases by one, because the number of trees increases. The amortized cost is thus still constant.

Operation **extract minimum** (same as _delete minimum_) operates in three phases. First we take the root containing the minimum element and remove it. Its children will become roots of new trees. If the number of children was _d_, it takes time _O_(_d_) to process all new roots and the potential increases by _d_−1. Therefore, the amortized running time of this phase is _O_(_d_) = _O_(log _n_).

However to complete the extract minimum operation, we need to update the pointer to the root with minimum key. Unfortunately there may be up to _n_ roots we need to check. In the second phase we therefore decrease the number of roots by successively linking together roots of the same degree. When two roots _u_ and _v_ have the same degree, we make one of them a child of the other so that the one with the smaller key remains the root. Its degree will increase by one. This is repeated until every root has a different degree. To find trees of the same degree efficiently we use an array of length _O_(log _n_) in which we keep a pointer to one root of each degree. When a second root is found of the same degree, the two are linked and the array is updated. The actual running time is _O_(log _n_ + _m_) where _m_ is the number of roots at the beginning of the second phase. At the end we will have at most _O_(log _n_) roots (because each has a different degree). Therefore, the difference in the potential function from before this phase to after it is: _O_(log _n_) − _m_, and the amortized running time is then at most _O_(log _n_ + _m_) + _c_(_O_(log _n_) − _m_). With a sufficiently large choice of _c_, this simplifies to _O_(log _n_).

<table style="margin: 0 auto;"><tbody><tr><td><img src="//upload.wikimedia.org/wikipedia/commons/thumb/5/56/Fibonacci_heap_extractmin1.png/170px-Fibonacci_heap_extractmin1.png" decoding="async" width="170" height="113" class="mw-file-element" srcset="//upload.wikimedia.org/wikipedia/commons/thumb/5/56/Fibonacci_heap_extractmin1.png/255px-Fibonacci_heap_extractmin1.png 1.5x, //upload.wikimedia.org/wikipedia/commons/5/56/Fibonacci_heap_extractmin1.png 2x" data-file-width="312" data-file-height="208">Figure 2. Fibonacci heap from Figure 1 after first phase of extract minimum. Node with key 1 (the minimum) was deleted and its children were added as separate trees.</td><td><img src="//upload.wikimedia.org/wikipedia/commons/thumb/9/95/Fibonacci_heap_extractmin2.png/130px-Fibonacci_heap_extractmin2.png" decoding="async" width="130" height="140" class="mw-file-element" srcset="//upload.wikimedia.org/wikipedia/commons/thumb/9/95/Fibonacci_heap_extractmin2.png/195px-Fibonacci_heap_extractmin2.png 1.5x, //upload.wikimedia.org/wikipedia/commons/9/95/Fibonacci_heap_extractmin2.png 2x" data-file-width="242" data-file-height="260">Figure 3. Fibonacci heap from Figure 1 after extract minimum is completed. First, nodes 3 and 6 are linked together. Then the result is linked with tree rooted at node 2. Finally, the new minimum is found.</td><td><img src="//upload.wikimedia.org/wikipedia/commons/thumb/0/09/Fibonacci_heap-decreasekey.png/250px-Fibonacci_heap-decreasekey.png" decoding="async" width="250" height="91" class="mw-file-element" srcset="//upload.wikimedia.org/wikipedia/commons/thumb/0/09/Fibonacci_heap-decreasekey.png/375px-Fibonacci_heap-decreasekey.png 1.5x, //upload.wikimedia.org/wikipedia/commons/thumb/0/09/Fibonacci_heap-decreasekey.png/500px-Fibonacci_heap-decreasekey.png 2x" data-file-width="518" data-file-height="188">Figure 4. Fibonacci heap from Figure 1 after decreasing key of node 9 to 0. This node as well as its two marked ancestors are cut from the tree rooted at 1 and placed as new roots.</td></tr></tbody></table>

In the third phase we check each of the remaining roots and find the minimum. This takes _O_(log _n_) time and the potential does not change. The overall amortized running time of extract minimum is therefore _O_(log _n_).

Operation **decrease key** will take the node, decrease the key and if the heap property becomes violated (the new key is smaller than the key of the parent), the node is cut from its parent. If the parent is not a root, it is marked. If it has been marked already, it is cut as well and its parent is marked. We continue upwards until we reach either the root or an unmarked node. Now we set the minimum pointer to the decreased value if it is the new minimum. In the process we create some number, say _k_, of new trees. Each of these new trees except possibly the first one was marked originally but as a root it will become unmarked. One node can become marked. Therefore, the number of marked nodes changes by −(_k_ − 1) + 1 = − _k_ + 2. Combining these 2 changes, the potential changes by 2 (−_k_ + 2) + _k_ = −_k_ + 4. The actual time to perform the cutting was _O_(_k_), therefore (again with a sufficiently large choice of _c_) the amortized running time is constant.

Finally, operation **delete** can be implemented simply by decreasing the key of the element to be deleted to minus infinity, thus turning it into the minimum of the whole heap. Then we call extract minimum to remove it. The amortized running time of this operation is _O_(log _n_).

Proof of degree bounds
----------------------

The amortized performance of a Fibonacci heap depends on the degree (number of children) of any tree root being _O_(log _n_), where _n_ is the size of the heap. Here we show that the size of the (sub) tree rooted at any node _x_ of degree _d_ in the heap must have size at least _Fd_+2, where _Fk_ is the _k_th Fibonacci number. The degree bound follows from this and the fact (easily proved by induction) that  ![F_{d+2}\geq \varphi ^{d}](https://wikimedia.org/api/rest_v1/media/math/render/svg/63432dfc502dac7cb7fd73b118d093f23e6b578f) for all integers  ![d\geq 0](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c3a2cca0c610ded6e0f188788933fb11f07df4), where  ![\varphi =(1+{\sqrt {5}})/2\doteq 1.618](https://wikimedia.org/api/rest_v1/media/math/render/svg/08e8c9a7d14a67697d5b11a2ff266c3fef21155e). (We then have  ![n\geq F_{d+2}\geq \varphi ^{d}](https://wikimedia.org/api/rest_v1/media/math/render/svg/4322d3c1d2f5a5e6f44cc247aa6c51ca5b091ea9), and taking the log to base  ![\varphi ](https://wikimedia.org/api/rest_v1/media/math/render/svg/33ee699558d09cf9d653f6351f9fda0b2f4aaa3e) of both sides gives  ![d\leq \log _{\varphi }n](https://wikimedia.org/api/rest_v1/media/math/render/svg/35f3899165d1758028c84525d49637f3cae1f832) as required.)

Consider any node _x_ somewhere in the heap (_x_ need not be the root of one of the main trees). Define **size**(_x_) to be the size of the tree rooted at _x_ (the number of descendants of _x_, including _x_ itself). We prove by induction on the height of _x_ (the length of a longest simple path from _x_ to a descendant leaf), that **size**(_x_) ≥ _Fd_+2, where _d_ is the degree of _x_.

**Base case:** If _x_ has height 0, then _d_ = 0, and **size**(_x_) = 1 = _F_2.

**Inductive case:** Suppose _x_ has positive height and degree _d_ > 0. Let _y_1, _y_2, ..., _yd_ be the children of _x_, indexed in order of the times they were most recently made children of _x_ (_y_1 being the earliest and _yd_ the latest), and let _c_1, _c_2, ..., _cd_ be their respective degrees. We **claim** that _ci_ ≥ _i_-2 for each _i_ with 2 ≤ _i_ ≤ _d_: Just before _yi_ was made a child of _x_, _y_1,...,_yi_−1 were already children of _x_, and so _x_ had degree at least _i_−1 at that time. Since trees are combined only when the degrees of their roots are equal, it must have been that _yi_ also had degree at least _i_-1 at the time it became a child of _x_. From that time to the present, _yi_ can only have lost at most one child (as guaranteed by the marking process), and so its current degree _ci_ is at least _i_−2. This proves the **claim**.

Since the heights of all the _yi_ are strictly less than that of _x_, we can apply the inductive hypothesis to them to get **size**(_yi_) ≥ _Fci_+2 ≥ _F_(_i_−2)+2 = _Fi_. The nodes _x_ and _y_1 each contribute at least 1 to **size**(_x_), and so we have

 ![{\textbf {size}}(x)\geq 2+\sum _{i=2}^{d}{\textbf {size}}(y_{i})\geq 2+\sum _{i=2}^{d}F_{i}=1+\sum _{i=0}^{d}F_{i}.](https://wikimedia.org/api/rest_v1/media/math/render/svg/feb69dc19583c0c49c159a3cf72e0d6fd0114788)

A routine induction proves that  ![1+\sum _{i=0}^{d}F_{i}=F_{d+2}](https://wikimedia.org/api/rest_v1/media/math/render/svg/e18bd86d3bb675e8a45c8a090862fe38e620c851) for any  ![d\geq 0](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c3a2cca0c610ded6e0f188788933fb11f07df4), which gives the desired lower bound on **size**(_x_).

Worst case
----------

Although Fibonacci heaps look very efficient, they have the following two drawbacks:

1.  They are complicated to implement.
2.  They are not as efficient in practice when compared with theoretically less efficient forms of heaps. In their simplest version they require storage and manipulation of four pointers per node, whereas only two or three pointers per node are needed in other structures, such as the binary heap, binomial heap, pairing heap, Brodal queue and rank-pairing heap.

Although the total running time of a sequence of operations starting with an empty structure is bounded by the bounds given above, some (very few) operations in the sequence can take very long to complete (in particular delete and delete minimum have linear running time in the worst case). For this reason Fibonacci heaps and other amortized data structures may not be appropriate for real-time systems. It is possible to create a data structure which has the same worst-case performance as the Fibonacci heap has amortized performance. One such structure, the Brodal queue, is, in the words of the creator, "quite complicated" and "[not] applicable in practice." Created in 2012, the strict Fibonacci heap is a simpler (compared to Brodal's) structure with the same worst-case bounds. Despite having simpler structure, experiments show that in practice the strict Fibonacci heap performs slower than more complicated Brodal queue and also slower than basic Fibonacci heap. The run-relaxed heaps of Driscoll et al. give good worst-case performance for all Fibonacci heap operations except merge.

Summary of running times
------------------------

Here are time complexities of various heap data structures. Function names assume a min-heap. For the meaning of "_O_(_f_)" and "_Θ_(_f_)" see Big O notation.

<table class="wikitable"><tbody><tr><th>Operation</th><th>find-min</th><th>delete-min</th><th>insert</th><th>decrease-key</th><th>meld</th></tr><tr><th>Binary</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>Θ</i>(log&nbsp;<i>n</i>)</td><td style="background:#ffffdd"><i>O</i>(log&nbsp;<i>n</i>)</td><td style="background:#ffffdd"><i>O</i>(log&nbsp;<i>n</i>)</td><td style="background:#ffdddd"><i>Θ</i>(<i>n</i>)</td></tr><tr><th>Leftist</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>Θ</i>(log <i>n</i>)</td><td style="background:#ffffdd"><i>Θ</i>(log <i>n</i>)</td><td style="background:#ffffdd"><i>O</i>(log <i>n</i>)</td><td style="background:#ffffdd"><i>Θ</i>(log <i>n</i>)</td></tr><tr><th>Binomial</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>Θ</i>(log <i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>Θ</i>(log <i>n</i>)</td><td style="background:#ffffdd"><i>O</i>(log&nbsp;<i>n</i>)</td></tr><tr><th>Skew binomial</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>Θ</i>(log <i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>Θ</i>(log <i>n</i>)</td><td style="background:#ffffdd"><i>O</i>(log&nbsp;<i>n</i>)</td></tr><tr><th>Pairing</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>O</i>(log <i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>o</i>(log&nbsp;<i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td></tr><tr><th>Rank-pairing</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>O</i>(log <i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td></tr><tr><th>Fibonacci</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>O</i>(log&nbsp;<i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td></tr><tr><th>Strict Fibonacci</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>O</i>(log <i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td></tr><tr><th>Brodal</th><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>O</i>(log&nbsp;<i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td></tr><tr><th>2–3 heap</th><td style="background:#ffffdd"><i>O</i>(log <i>n</i>)</td><td style="background:#ffffdd"><i>O</i>(log <i>n</i>)</td><td style="background:#ffffdd"><i>O</i>(log <i>n</i>)</td><td style="background:#ddffdd"><i>Θ</i>(1)</td><td style="background:#ffffdd"><i>?</i></td></tr></tbody></table>

1.  ^ Jump up to: a b c d e f g h i Amortized time.
2.  **^** Brodal and Okasaki describe a technique to reduce the worst-case complexity of _meld_ to _Θ_(1); this technique applies to any heap datastructure that has _insert_ in _Θ_(1) and _find-min_, _delete-min_, _meld_ in _O_(log _n_).
3.  **^** Lower bound of  ![{\displaystyle \Omega (\log \log n),}](https://wikimedia.org/api/rest_v1/media/math/render/svg/7caf8c32fbd99eed1338c02b7f7f1255f16ade36) upper bound of  ![{\displaystyle O(2^{2{\sqrt {\log \log n}}}).}](https://wikimedia.org/api/rest_v1/media/math/render/svg/45c772d2ec070e49a6438ea92b1c8dc764613c5e)
4.  **^** Brodal and Okasaki later describe a persistent variant with the same bounds except for decrease-key, which is not supported. Heaps with _n_ elements can be constructed bottom-up in _O_(_n_).

Practical considerations
------------------------

Fibonacci heaps have a reputation for being slow in practice due to large memory consumption per node and high constant factors on all operations. Recent experimental results suggest that Fibonacci heaps are more efficient in practice than most of its later derivatives, including quake heaps, violation heaps, strict Fibonacci heaps, rank pairing heaps, but less efficient than either pairing heaps or array-based heaps.

References
----------

1.  **^** Third edition p. 518.
2.  ^ Jump up to: a b c Fredman, Michael Lawrence; Tarjan, Robert E. (July 1987). "Fibonacci heaps and their uses in improved network optimization algorithms" (PDF). _Journal of the Association for Computing Machinery_. **34** (3): 596–615. CiteSeerX 10.1.1.309.8927. doi: 10.1145/28869.28874.
3.  **^** Fredman, Michael L.; Sedgewick, Robert; Sleator, Daniel D.; Tarjan, Robert E. (1986). "The pairing heap: a new form of self-adjusting heap" (PDF). _Algorithmica_. **1** (1–4): 111–129. doi: 10.1007/BF01840439. S2CID 23664143.
4.  **^** Gerth Stølting Brodal (1996), "Worst-Case Efficient Priority Queues", _Proc. 7th ACM-SIAM Symposium on Discrete Algorithms_, Society for Industrial and Applied Mathematics: 52–58, CiteSeerX 10.1.1.43.8133, ISBN 0-89871-366-8
5.  **^** Brodal, G. S. L.; Lagogiannis, G.; Tarjan, R. E. (2012). Strict Fibonacci heaps (PDF). Proceedings of the 44th symposium on Theory of Computing - STOC '12. p. 1177. doi: 10.1145/2213977.2214082. ISBN 978-1-4503-1245-5.
6.  **^** Mrena, Michal; Sedlacek, Peter; Kvassay, Miroslav (June 2019). "Practical Applicability of Advanced Implementations of Priority Queues in Finding Shortest Paths". _2019 International Conference on Information and Digital Technologies (IDT)_. Zilina, Slovakia: IEEE. pp. 335–344. doi: 10.1109/DT. 2019.8813457. ISBN 9781728114019. S2CID 201812705.
7.  ^ Jump up to: a b Larkin, Daniel; Sen, Siddhartha; Tarjan, Robert (2014). "A Back-to-Basics Empirical Study of Priority Queues". _Proceedings of the Sixteenth Workshop on Algorithm Engineering and Experiments_: 61–72. arXiv: 1403.0252. Bibcode: 2014arXiv1403.0252L. doi: 10.1137/1.9781611973198.7. ISBN 978-1-61197-319-8. S2CID 15216766.
8.  **^** Driscoll, James R.; Gabow, Harold N.; Shrairman, Ruth; Tarjan, Robert E. (November 1988). "Relaxed heaps: An alternative to Fibonacci heaps with applications to parallel computation" (PDF). _Communications of the ACM_. **31** (11): 1343–1354. doi: 10.1145/50087.50096. S2CID 16078067.
9.  ^ Jump up to: a b c d Cormen, Thomas H.; Leiserson, Charles E.; Rivest, Ronald L. (1990). Introduction to Algorithms (1st ed.). MIT Press and McGraw-Hill. ISBN 0-262-03141-8.
10.  **^** "Binomial Heap | Brilliant Math & Science Wiki". _brilliant. org_. Retrieved 2019-09-30.
11.  **^** Brodal, Gerth Stølting; Okasaki, Chris (November 1996), "Optimal purely functional priority queues", _Journal of Functional Programming_, **6** (6): 839–857, doi: 10.1017/s095679680000201x
12.  **^** Iacono, John (2000), "Improved upper bounds for pairing heaps", Proc. 7th Scandinavian Workshop on Algorithm Theory (PDF), Lecture Notes in Computer Science, vol. 1851, Springer-Verlag, pp. 63–77, arXiv: 1110.4428, CiteSeerX 10.1.1.748.7812, doi: 10.1007/3-540-44985-X_5, ISBN 3-540-67690-2
13.  **^** Fredman, Michael Lawrence (July 1999). "On the Efficiency of Pairing Heaps and Related Data Structures" (PDF). _Journal of the Association for Computing Machinery_. **46** (4): 473–501. doi: 10.1145/320211.320214.
14.  **^** Pettie, Seth (2005). Towards a Final Analysis of Pairing Heaps (PDF). FOCS '05 Proceedings of the 46th Annual IEEE Symposium on Foundations of Computer Science. pp. 174–183. CiteSeerX 10.1.1.549.471. doi: 10.1109/SFCS. 2005.75. ISBN 0-7695-2468-0.
15.  **^** Haeupler, Bernhard; Sen, Siddhartha; Tarjan, Robert E. (November 2011). "Rank-pairing heaps" (PDF). _SIAM J. Computing_. **40** (6): 1463–1485. doi: 10.1137/100785351.
16.  **^** Brodal, Gerth Stølting; Lagogiannis, George; Tarjan, Robert E. (2012). Strict Fibonacci heaps (PDF). Proceedings of the 44th symposium on Theory of Computing - STOC '12. pp. 1177–1184. CiteSeerX 10.1.1.233.1740. doi: 10.1145/2213977.2214082. ISBN 978-1-4503-1245-5.
17.  **^** Brodal, Gerth S. (1996), "Worst-Case Efficient Priority Queues" (PDF), _Proc. 7th Annual ACM-SIAM Symposium on Discrete Algorithms_, pp. 52–58
18.  **^** Goodrich, Michael T.; Tamassia, Roberto (2004). "7.3.6. Bottom-Up Heap Construction". _Data Structures and Algorithms in Java_ (3rd ed.). pp. 338–341. ISBN 0-471-46983-1.
19.  **^** Takaoka, Tadao (1999), Theory of 2–3 Heaps (PDF), p. 12
20.  **^** http://www.cs.princeton.edu/~wayne/kleinberg-tardos/pdf/FibonacciHeaps.pdf , p. 79

External links
--------------

*   Java applet simulation of a Fibonacci heap
*   MATLAB implementation of Fibonacci heap
*   De-recursived and memory efficient C implementation of Fibonacci heap (free/libre software, CeCILL-B license)
*   Ruby implementation of the Fibonacci heap (with tests)
*   Pseudocode of the Fibonacci heap algorithm
*   Various Java Implementations for Fibonacci heap

*   Collection
*   Container

Abstract

*   Associative array
    *   Multimap
    *   Retrieval Data Structure
*   List
*   Stack
*   Queue
    *   Double-ended queue
*   Priority queue
    *   Double-ended priority queue
*   Set
    *   Multiset
    *   Disjoint-set

Arrays

*   Bit array
*   Circular buffer
*   Dynamic array
*   Hash table
*   Hashed array tree
*   Sparse matrix

Linked

*   Association list
*   Linked list
*   Skip list
*   Unrolled linked list
*   XOR linked list

Trees

*   B-tree
*   Binary search tree
    *   AA tree
    *   AVL tree
    *   Red–black tree
    *   Self-balancing tree
    *   Splay tree
*   Heap
    *   Binary heap
    *   Binomial heap
    *   Fibonacci heap
*   R-tree
    *   R* tree
    *   R+ tree
    *   Hilbert R-tree
*   Trie
    *   Hash tree

Graphs

*   Binary decision diagram
*   Directed acyclic graph
*   Directed acyclic word graph

*   List of data structures