## Introduction
| Operation    | Average  | Worst case |
| ------------ | -------- | ---------- |
| Insert       | Θ(1)     |            |
| Find-min     | Θ(1)     |            |
| Delete-min   | O (logn) |            |
| Decrease-key | Θ(1)     |            |
| Merge        | Θ(1)     |            |

In computer science, a Fibonacci heap is a data structure for priority queue operations, consisting of a collection of heap-ordered trees. It has a better amortized running time than many other priority queue data structures including the binary heap and binomial heap. Michael L. Fredman and Robert E. Tarjan developed Fibonacci heaps in 1984 and published them in a scientific journal in 1987. Fibonacci heaps are named after the Fibonacci numbers, which are used in their running time analysis.

For the Fibonacci heap, the find-minimum operation takes constant (O(1)) amortized time.[1] The insert and decrease key operations also work in constant amortized time.[2] Deleting an element (most often used in the special case of deleting the minimum element) works in O(log n) amortized time, where n is the size of the heap.[2] This means that starting from an empty data structure, any sequence of a insert and decrease key operations and b delete operations would take O(a + b log n) worst case time, where n is the maximum heap size. In a binary or binomial heap, such a sequence of operations would take O((a + b) log n) time. A Fibonacci heap is thus better than a binary or binomial heap when b is smaller than a by a non-constant factor. It is also possible to merge two Fibonacci heaps in constant amortized time, improving on the logarithmic merge time of a binomial heap, and improving on binary heaps which cannot handle merges efficiently.

Using Fibonacci heaps for priority queues improves the asymptotic running time of important algorithms, such as Dijkstra's algorithm for computing the shortest path between two nodes in a graph, compared to the same algorithm using other slower priority queue data structures.

## Structure
A Fibonacci heap is a collection of trees satisfying the minimum-heap property, that is, the key of a child is always greater than or equal to the key of the parent. This implies that the minimum key is always at the root of one of the trees. Compared with binomial heaps, the structure of a Fibonacci heap is more flexible. The trees do not have a prescribed shape and in the extreme case the heap can have every element in a separate tree. This flexibility allows some operations to be executed in a lazy manner, postponing the work for later operations. For example, merging heaps is done simply by concatenating the two lists of trees, and operation decrease key sometimes cuts a node from its parent and forms a new tree.

However, at some point order needs to be introduced to the heap to achieve the desired running time. In particular, degrees of nodes (here degree means the number of direct children) are kept quite low: every node has degree at most log n and the size of a subtree rooted in a node of degree k is at least Fk+2, where Fk is the kth Fibonacci number. This is achieved by the rule that we can cut at most one child of each non-root node. When a second child is cut, the node itself needs to be cut from its parent and becomes the root of a new tree (see Proof of degree bounds, below). The number of trees is decreased in the operation delete minimum, where trees are linked together.

As a result of a relaxed structure, some operations can take a long time while others are done very quickly. For the amortized running time analysis, we use the potential method, in that we pretend that very fast operations take a little bit longer than they actually do. This additional time is then later combined and subtracted from the actual running time of slow operations. The amount of time saved for later use is measured at any given moment by a potential function. The potential of a Fibonacci heap is given by

Potential = t + 2m
where t is the number of trees in the Fibonacci heap, and m is the number of marked nodes. A node is marked if at least one of its children was cut since this node was made a child of another node (all roots are unmarked). The amortized time for an operation is given by the sum of the actual time and c times the difference in potential, where c is a constant (chosen to match the constant factors in the O notation for the actual time).

Thus, the root of each tree in a heap has one unit of time stored. This unit of time can be used later to link this tree with another tree at amortized time 0. Also, each marked node has two units of time stored. One can be used to cut the node from its parent. If this happens, the node becomes a root and the second unit of time will remain stored in it as in any other root.

## Implementation of operations
To allow fast deletion and concatenation, the roots of all trees are linked using a circular [doubly linked list]( https://en.wikipedia.org/wiki/Doubly_linked_list "Doubly linked list"). The children of each node are also linked using such a list. For each node, we maintain its number of children and whether the node is marked. Moreover, we maintain a pointer to the root containing the minimum key.

Operation **find minimum** is now trivial because we keep the pointer to the node containing it. It does not change the potential of the heap, therefore both actual and amortized cost are constant.

As mentioned above, **merge** is implemented simply by concatenating the lists of tree roots of the two heaps. This can be done in constant time and the potential does not change, leading again to constant amortized time.

Operation **insert** works by creating a new heap with one element and doing merge. This takes constant time, and the potential increases by one, because the number of trees increases. The amortized cost is thus still constant.

Operation **extract minimum** (same as _delete minimum_) operates in three phases. First we take the root containing the minimum element and remove it. Its children will become roots of new trees. If the number of children was _d_, it takes time _O_(_d_) to process all new roots and the potential increases by _d_−1. Therefore, the amortized running time of this phase is _O_(_d_) = _O_(log _n_).

However to complete the extract minimum operation, we need to update the pointer to the root with minimum key. Unfortunately there may be up to _n_ roots we need to check. In the second phase we therefore decrease the number of roots by successively linking together roots of the same degree. When two roots _u_ and _v_ have the same degree, we make one of them a child of the other so that the one with the smaller key remains the root. Its degree will increase by one. This is repeated until every root has a different degree. To find trees of the same degree efficiently we use an array of length _O_(log _n_) in which we keep a pointer to one root of each degree. When a second root is found of the same degree, the two are linked and the array is updated. The actual running time is _O_(log _n_ + _m_) where _m_ is the number of roots at the beginning of the second phase. At the end we will have at most _O_(log _n_) roots (because each has a different degree). Therefore, the difference in the potential function from before this phase to after it is: _O_(log _n_) − _m_, and the amortized running time is then at most _O_(log _n_ + _m_) + _c_(_O_(log _n_) − _m_). With a sufficiently large choice of _c_, this simplifies to _O_(log _n_).

![[93-Fibonacci-heap-figure-234.png]]

In the third phase we check each of the remaining roots and find the minimum. This takes _O_(log _n_) time and the potential does not change. The overall amortized running time of extract minimum is therefore _O_(log _n_).

Operation **decrease key** will take the node, decrease the key and if the heap property becomes violated (the new key is smaller than the key of the parent), the node is cut from its parent. If the parent is not a root, it is marked. If it has been marked already, it is cut as well and its parent is marked. We continue upwards until we reach either the root or an unmarked node. Now we set the minimum pointer to the decreased value if it is the new minimum. In the process we create some number, say _k_, of new trees. Each of these new trees except possibly the first one was marked originally but as a root it will become unmarked. One node can become marked. Therefore, the number of marked nodes changes by −(_k_ − 1) + 1 = − _k_ + 2. Combining these 2 changes, the potential changes by 2(−_k_ + 2) + _k_ = −_k_ + 4. The actual time to perform the cutting was _O_(_k_), therefore (again with a sufficiently large choice of _c_) the amortized running time is constant.

Finally, operation **delete** can be implemented simply by decreasing the key of the element to be deleted to minus infinity, thus turning it into the minimum of the whole heap. Then we call extract minimum to remove it. The amortized running time of this operation is _O_(log _n_).

## Proof of degree bounds
The amortized performance of a Fibonacci heap depends on the degree (number of children) of any tree root being O(log n), where n is the size of the heap. Here we show that the size of the (sub)tree rooted at any node x of degree d in the heap must have size at least Fd+2, where Fk is the kth Fibonacci number. The degree bound follows from this and the fact (easily proved by induction) that 
�
�
+
2
≥
�
�
F_{d+2}\geq \varphi ^{d} for all integers 
�
≥
0
d\geq 0, where 
�
=
(
1
+
5
)
/
2
≐
1.618
\varphi =(1+{\sqrt {5}})/2\doteq 1.618. (We then have 
�
≥
�
�
+
2
≥
�
�
n\geq F_{d+2}\geq \varphi ^{d}, and taking the log to base 
�\varphi  of both sides gives 
�
≤
log
�
⁡
�
d\leq \log _{\varphi }n as required.)

Consider any node x somewhere in the heap (x need not be the root of one of the main trees). Define size(x) to be the size of the tree rooted at x (the number of descendants of x, including x itself). We prove by induction on the height of x (the length of a longest simple path from x to a descendant leaf), that size(x) ≥ Fd+2, where d is the degree of x.

Base case: If x has height 0, then d = 0, and size(x) = 1 = F2.

Inductive case: Suppose x has positive height and degree d > 0. Let y1, y2, ..., yd be the children of x, indexed in order of the times they were most recently made children of x (y1 being the earliest and yd the latest), and let c1, c2, ..., cd be their respective degrees. We claim that ci ≥ i-2 for each i with 2 ≤ i ≤ d: Just before yi was made a child of x, y1,...,yi−1 were already children of x, and so x had degree at least i−1 at that time. Since trees are combined only when the degrees of their roots are equal, it must have been that yi also had degree at least i-1 at the time it became a child of x. From that time to the present, yi can only have lost at most one child (as guaranteed by the marking process), and so its current degree ci is at least i−2. This proves the claim.

Since the heights of all the yi are strictly less than that of x, we can apply the inductive hypothesis to them to get size(yi) ≥ Fci+2 ≥ F(i−2)+2 = Fi. The nodes x and y1 each contribute at least 1 to size(x), and so we have

size
(
�
)
≥
2
+
∑
�
=
2
�
size
(
�
�
)
≥
2
+
∑
�
=
2
�
�
�
=
1
+
∑
�
=
0
�
�
�
.
{\textbf {size}}(x)\geq 2+\sum _{i=2}^{d}{\textbf {size}}(y_{i})\geq 2+\sum _{i=2}^{d}F_{i}=1+\sum _{i=0}^{d}F_{i}.

A routine induction proves that 
1
+
∑
�
=
0
�
�
�
=
�
�
+
2
1+\sum _{i=0}^{d}F_{i}=F_{d+2} for any 
�
≥
0
d\geq 0, which gives the desired lower bound on size(x).

## Worst case
Although Fibonacci heaps look very efficient, they have the following two drawbacks:[3]

They are complicated to implement.
They are not as efficient in practice when compared with theoretically less efficient forms of heaps. In their simplest version they require storage and manipulation of four pointers per node, whereas only two or three pointers per node are needed in other structures, such as the binary heap, binomial heap, pairing heap, Brodal queue and rank-pairing heap.
Although the total running time of a sequence of operations starting with an empty structure is bounded by the bounds given above, some (very few) operations in the sequence can take very long to complete (in particular delete and delete minimum have linear running time in the worst case). For this reason Fibonacci heaps and other amortized data structures may not be appropriate for real-time systems. It is possible to create a data structure which has the same worst-case performance as the Fibonacci heap has amortized performance. One such structure, the Brodal queue,[4] is, in the words of the creator, "quite complicated" and "[not] applicable in practice." Created in 2012, the strict Fibonacci heap[5] is a simpler (compared to Brodal's) structure with the same worst-case bounds. Despite having simpler structure, experiments show that in practice the strict Fibonacci heap performs slower than more complicated Brodal queue and also slower than basic Fibonacci heap.[6][7] The run-relaxed heaps of Driscoll et al. give good worst-case performance for all Fibonacci heap operations except merge.[8]

## Summary of various heaps' running time
![[93-Fibonacci-heap-running-time-summary.png]]

## Reference
1.  [Cormen, Thomas H.]( https://en.wikipedia.org/wiki/Thomas_H._Cormen "Thomas H. Cormen"); [Leiserson, Charles E.]( https://en.wikipedia.org/wiki/Charles_E._Leiserson "Charles E. Leiserson"); [Rivest, Ronald L.]( https://en.wikipedia.org/wiki/Ron_Rivest "Ron Rivest"); [Stein, Clifford]( https://en.wikipedia.org/wiki/Clifford_Stein "Clifford Stein") (2001) [1990]. "Chapter 20: Fibonacci Heaps". [_Introduction to Algorithms_]( https://en.wikipedia.org/wiki/Introduction_to_Algorithms "Introduction to Algorithms") (2nd ed.). MIT Press and McGraw-Hill. pp. 476–497. [ISBN]( https://en.wikipedia.org/wiki/ISBN_ (identifier) "ISBN (identifier)") [0-262-03293-7]( https://en.wikipedia.org/wiki/Special:BookSources/0-262-03293-7 "Special: BookSources/0-262-03293-7"). Third edition p. 518.
2. ^ [Jump up to:_**a**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-Fredman_And_Tarjan_2-0) [_**b**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-Fredman_And_Tarjan_2-1) [_**c**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-Fredman_And_Tarjan_2-2) [Fredman, Michael Lawrence](https://en.wikipedia.org/wiki/Michael_Fredman "Michael Fredman"); [Tarjan, Robert E.](https://en.wikipedia.org/wiki/Robert_Tarjan "Robert Tarjan") (July 1987). ["Fibonacci heaps and their uses in improved network optimization algorithms"](http://bioinfo.ict.ac.cn/~dbu/AlgorithmCourses/Lectures/Fibonacci-Heap-Tarjan.pdf) (PDF). _[Journal of the Association for Computing Machinery](https://en.wikipedia.org/wiki/Journal_of_the_Association_for_Computing_Machinery "Journal of the Association for Computing Machinery")_. **34** (3): 596–615. [CiteSeerX](https://en.wikipedia.org/wiki/CiteSeerX_(identifier) "CiteSeerX (identifier)") [10.1.1.309.8927](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.309.8927). [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1145/28869.28874](https://doi.org/10.1145%2F28869.28874).
3. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-FSST_3-0 "Jump up")** [Fredman, Michael L.](https://en.wikipedia.org/wiki/Michael_Fredman "Michael Fredman"); [Sedgewick, Robert](https://en.wikipedia.org/wiki/Robert_Sedgewick_(computer_scientist) "Robert Sedgewick (computer scientist)"); [Sleator, Daniel D.](https://en.wikipedia.org/wiki/Daniel_Sleator "Daniel Sleator"); [Tarjan, Robert E.](https://en.wikipedia.org/wiki/Robert_Tarjan "Robert Tarjan") (1986). ["The pairing heap: a new form of self-adjusting heap"](https://www.cs.cmu.edu/~sleator/papers/pairing-heaps.pdf) (PDF). _Algorithmica_. **1** (1–4): 111–129. [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1007/BF01840439](https://doi.org/10.1007%2FBF01840439). [S2CID](https://en.wikipedia.org/wiki/S2CID_(identifier) "S2CID (identifier)") [23664143](https://api.semanticscholar.org/CorpusID:23664143).
4. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-bare_url_4-0 "Jump up")** Gerth Stølting Brodal (1996), ["Worst-Case Efficient Priority Queues"](https://archive.org/details/proceedingsofsev0000acms/page/52), _Proc. 7th ACM-SIAM Symposium on Discrete Algorithms_, [Society for Industrial and Applied Mathematics](https://en.wikipedia.org/wiki/Society_for_Industrial_and_Applied_Mathematics "Society for Industrial and Applied Mathematics"): 52–58, [CiteSeerX](https://en.wikipedia.org/wiki/CiteSeerX_(identifier) "CiteSeerX (identifier)") [10.1.1.43.8133](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.43.8133), [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [0-89871-366-8](https://en.wikipedia.org/wiki/Special:BookSources/0-89871-366-8 "Special:BookSources/0-89871-366-8")
5. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-5 "Jump up")** Brodal, G. S. L.; Lagogiannis, G.; Tarjan, R. E. (2012). [_Strict Fibonacci heaps_](http://www.cs.au.dk/~gerth/papers/stoc12.pdf) (PDF). Proceedings of the 44th symposium on Theory of Computing - STOC '12. p. 1177. [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1145/2213977.2214082](https://doi.org/10.1145%2F2213977.2214082). [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [978-1-4503-1245-5](https://en.wikipedia.org/wiki/Special:BookSources/978-1-4503-1245-5 "Special:BookSources/978-1-4503-1245-5").
6. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-6 "Jump up")** Mrena, Michal; Sedlacek, Peter; Kvassay, Miroslav (June 2019). "Practical Applicability of Advanced Implementations of Priority Queues in Finding Shortest Paths". _2019 International Conference on Information and Digital Technologies (IDT)_. Zilina, Slovakia: IEEE. pp. 335–344. [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1109/DT.2019.8813457](https://doi.org/10.1109%2FDT.2019.8813457). [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [9781728114019](https://en.wikipedia.org/wiki/Special:BookSources/9781728114019 "Special:BookSources/9781728114019"). [S2CID](https://en.wikipedia.org/wiki/S2CID_(identifier) "S2CID (identifier)") [201812705](https://api.semanticscholar.org/CorpusID:201812705).
7. ^ [Jump up to:_**a**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-:0_7-0) [_**b**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-:0_7-1) Larkin, Daniel; Sen, Siddhartha; Tarjan, Robert (2014). "A Back-to-Basics Empirical Study of Priority Queues". _Proceedings of the Sixteenth Workshop on Algorithm Engineering and Experiments_: 61–72. [arXiv](https://en.wikipedia.org/wiki/ArXiv_(identifier) "ArXiv (identifier)"):[1403.0252](https://arxiv.org/abs/1403.0252). [Bibcode](https://en.wikipedia.org/wiki/Bibcode_(identifier) "Bibcode (identifier)"):[2014arXiv1403.0252L](https://ui.adsabs.harvard.edu/abs/2014arXiv1403.0252L). [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1137/1.9781611973198.7](https://doi.org/10.1137%2F1.9781611973198.7). [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [978-1-61197-319-8](https://en.wikipedia.org/wiki/Special:BookSources/978-1-61197-319-8 "Special:BookSources/978-1-61197-319-8"). [S2CID](https://en.wikipedia.org/wiki/S2CID_(identifier) "S2CID (identifier)") [15216766](https://api.semanticscholar.org/CorpusID:15216766).
8. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-driscoll_8-0 "Jump up")** Driscoll, James R.; Gabow, Harold N.; Shrairman, Ruth; Tarjan, Robert E. (November 1988). ["Relaxed heaps: An alternative to Fibonacci heaps with applications to parallel computation"](https://dl.acm.org/doi/pdf/10.1145/50087.50096) (PDF). _Communications of the ACM_. **31** (11): 1343–1354. [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1145/50087.50096](https://doi.org/10.1145%2F50087.50096). [S2CID](https://en.wikipedia.org/wiki/S2CID_(identifier) "S2CID (identifier)") [16078067](https://api.semanticscholar.org/CorpusID:16078067).
9. ^ [Jump up to:_**a**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-CLRS_9-0) [_**b**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-CLRS_9-1) [_**c**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-CLRS_9-2) [_**d**_](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-CLRS_9-3) [Cormen, Thomas H.](https://en.wikipedia.org/wiki/Thomas_H._Cormen "Thomas H. Cormen"); [Leiserson, Charles E.](https://en.wikipedia.org/wiki/Charles_E._Leiserson "Charles E. Leiserson"); [Rivest, Ronald L.](https://en.wikipedia.org/wiki/Ron_Rivest "Ron Rivest") (1990). [_Introduction to Algorithms_](https://en.wikipedia.org/wiki/Introduction_to_Algorithms "Introduction to Algorithms") (1st ed.). MIT Press and McGraw-Hill. [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [0-262-03141-8](https://en.wikipedia.org/wiki/Special:BookSources/0-262-03141-8 "Special:BookSources/0-262-03141-8").
10. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-10 "Jump up")** ["Binomial Heap | Brilliant Math & Science Wiki"](https://brilliant.org/wiki/binomial-heap/). _brilliant.org_. Retrieved 2019-09-30.
11. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-12 "Jump up")** Brodal, Gerth Stølting; Okasaki, Chris (November 1996), "Optimal purely functional priority queues", _Journal of Functional Programming_, **6** (6): 839–857, [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1017/s095679680000201x](https://doi.org/10.1017%2Fs095679680000201x)
12. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-Iacono_14-0 "Jump up")** [Iacono, John](https://en.wikipedia.org/wiki/John_Iacono "John Iacono") (2000), "Improved upper bounds for pairing heaps", [_Proc. 7th Scandinavian Workshop on Algorithm Theory_](http://john2.poly.edu/papers/swat00/paper.pdf) (PDF), Lecture Notes in Computer Science, vol. 1851, Springer-Verlag, pp. 63–77, [arXiv](https://en.wikipedia.org/wiki/ArXiv_(identifier) "ArXiv (identifier)"):[1110.4428](https://arxiv.org/abs/1110.4428), [CiteSeerX](https://en.wikipedia.org/wiki/CiteSeerX_(identifier) "CiteSeerX (identifier)") [10.1.1.748.7812](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.748.7812), [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1007/3-540-44985-X_5](https://doi.org/10.1007%2F3-540-44985-X_5), [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [3-540-67690-2](https://en.wikipedia.org/wiki/Special:BookSources/3-540-67690-2 "Special:BookSources/3-540-67690-2")
13. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-Fredman1999_15-0 "Jump up")** [Fredman, Michael Lawrence](https://en.wikipedia.org/wiki/Michael_Fredman "Michael Fredman") (July 1999). ["On the Efficiency of Pairing Heaps and Related Data Structures"](http://www.uqac.ca/azinflou/Fichiers840/EfficiencyPairingHeap.pdf) (PDF). _[Journal of the Association for Computing Machinery](https://en.wikipedia.org/wiki/Journal_of_the_Association_for_Computing_Machinery "Journal of the Association for Computing Machinery")_. **46** (4): 473–501. [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1145/320211.320214](https://doi.org/10.1145%2F320211.320214).
14. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-16 "Jump up")** Pettie, Seth (2005). [_Towards a Final Analysis of Pairing Heaps_](http://web.eecs.umich.edu/~pettie/papers/focs05.pdf) (PDF). FOCS '05 Proceedings of the 46th Annual IEEE Symposium on Foundations of Computer Science. pp. 174–183. [CiteSeerX](https://en.wikipedia.org/wiki/CiteSeerX_(identifier) "CiteSeerX (identifier)") [10.1.1.549.471](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.549.471). [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1109/SFCS.2005.75](https://doi.org/10.1109%2FSFCS.2005.75). [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [0-7695-2468-0](https://en.wikipedia.org/wiki/Special:BookSources/0-7695-2468-0 "Special:BookSources/0-7695-2468-0").
15. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-18 "Jump up")** Haeupler, Bernhard; Sen, Siddhartha; [Tarjan, Robert E.](https://en.wikipedia.org/wiki/Robert_Tarjan "Robert Tarjan") (November 2011). ["Rank-pairing heaps"](http://sidsen.org/papers/rp-heaps-journal.pdf) (PDF). _SIAM J. Computing_. **40** (6): 1463–1485. [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1137/100785351](https://doi.org/10.1137%2F100785351).
16. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-19 "Jump up")** [Brodal, Gerth Stølting](https://en.wikipedia.org/wiki/Gerth_St%C3%B8lting_Brodal "Gerth Stølting Brodal"); Lagogiannis, George; [Tarjan, Robert E.](https://en.wikipedia.org/wiki/Robert_Tarjan "Robert Tarjan") (2012). [_Strict Fibonacci heaps_](http://www.cs.au.dk/~gerth/papers/stoc12.pdf) (PDF). Proceedings of the 44th symposium on Theory of Computing - STOC '12. pp. 1177–1184. [CiteSeerX](https://en.wikipedia.org/wiki/CiteSeerX_(identifier) "CiteSeerX (identifier)") [10.1.1.233.1740](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.233.1740). [doi](https://en.wikipedia.org/wiki/Doi_(identifier) "Doi (identifier)"):[10.1145/2213977.2214082](https://doi.org/10.1145%2F2213977.2214082). [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [978-1-4503-1245-5](https://en.wikipedia.org/wiki/Special:BookSources/978-1-4503-1245-5 "Special:BookSources/978-1-4503-1245-5").
17. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-20 "Jump up")** [Brodal, Gerth S.](https://en.wikipedia.org/wiki/Gerth_St%C3%B8lting_Brodal "Gerth Stølting Brodal") (1996), ["Worst-Case Efficient Priority Queues"](http://www.cs.au.dk/~gerth/papers/soda96.pdf) (PDF), _Proc. 7th Annual ACM-SIAM Symposium on Discrete Algorithms_, pp. 52–58
18. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-21 "Jump up")** [Goodrich, Michael T.](https://en.wikipedia.org/wiki/Michael_T._Goodrich "Michael T. Goodrich"); [Tamassia, Roberto](https://en.wikipedia.org/wiki/Roberto_Tamassia "Roberto Tamassia") (2004). "7.3.6. Bottom-Up Heap Construction". _Data Structures and Algorithms in Java_ (3rd ed.). pp. 338–341. [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [0-471-46983-1](https://en.wikipedia.org/wiki/Special:BookSources/0-471-46983-1 "Special:BookSources/0-471-46983-1").
19. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-23 "Jump up")** [Takaoka, Tadao](https://en.wikipedia.org/w/index.php?title=Tadao_Takaoka&action=edit&redlink=1 "Tadao Takaoka (page does not exist)") (1999), [_Theory of 2–3 Heaps_](https://ir.canterbury.ac.nz/bitstream/handle/10092/14769/2-3heaps.pdf) (PDF), p. 12
20. **[^](https://en.wikipedia.org/wiki/Fibonacci_heap#cite_ref-24 "Jump up")** [http://www.cs.princeton.edu/~wayne/kleinberg-tardos/pdf/FibonacciHeaps.pdf](http://www.cs.princeton.edu/~wayne/kleinberg-tardos/pdf/FibonacciHeaps.pdf), p. 79