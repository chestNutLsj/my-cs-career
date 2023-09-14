In combinatorial mathematics, the **Catalan numbers** are a sequence that occur in various counting problems, often involving recursively defined objects. They are named after the French-Belgian mathematician Eugène Charles Catalan.

The _n_th Catalan number can be expressed directly in terms of the [central binomial coefficients](/wiki/Central_binomial_coefficient "Central binomial coefficient") by

$$
Catalan(n)=
$$

The first Catalan numbers for _n_ = 0, 1, 2, 3, ... are

1, 1, 2, 5, 14, 42, 132, 429, 1430, 4862, 16796, 58786, ... (sequence [A000108]( https://oeis. org/A000108 "oeis: A000108") in the [OEIS](/wiki/On-Line_Encyclopedia_of_Integer_Sequences "On-Line Encyclopedia of Integer Sequences")).

## Properties
An alternative expression for $C_{n}$ is

for ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d02d53378dc43f8e0ad4cd7634d2292118567321)

which is equivalent to the expression given above because ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/6530a0ff779f2ee58e008583911960e54257f6f0). This expression shows that _C__n_ is an [integer](/wiki/Integer "Integer"), which is not immediately obvious from the first formula given. This expression forms the basis for a [proof of the correctness of the formula](#Second_proof).

Another alternative expression is

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/47986546821fac078d634e3d7d5c61c3f555033f)

which can be directly interpreted in terms of the [cycle lemma](/wiki/Cycle_lemma "Cycle lemma"); see below.

The Catalan numbers satisfy the [recurrence relations](/wiki/Recurrence_relation "Recurrence relation")

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/65dd2bfd4f5a5a12e17ddc5fa103d024dad06e9f)

and

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a080be87b162c637dffd2e710367cf6a0b082589)

Asymptotically, the Catalan numbers grow as

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/16a971c1a6b0535339d4c024dfb0ba79a06d93b6)

in the sense that the quotient of the

_n_

th Catalan number and the expression on the right tends towards 1 as

_n_

approaches infinity. A more accurate asymptotic analysis shows that the Catalan numbers are approximated by the fourth order approximation

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/5b0060e22fd8c64126aa2072336ad3be73ef2c40)

.

This can be proved by using the [asymptotic growth of the central binomial coefficients](/wiki/Central_binomial_coefficient #Asymptotic_growth "Central binomial coefficient"), by [Stirling's approximation](/wiki/Stirling%27s_approximation "Stirling's approximation") for ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/bae971720be3cc9b8d82f4cdac89cb89877514a6), or [via generating functions](/wiki/Generating_function #Asymptotic_growth_of_the_Catalan_numbers "Generating function").

The only Catalan numbers _C__n_ that are odd are those for which _n_ = 2_k_ − 1; all others are even. The only prime Catalan numbers are _C_2 = 2 and _C_3 = 5. [[1]] ( #cite_note -1)

The Catalan numbers have the integral representations

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/69cb170013fa8a87056f9e9241c6d74722161060)

which immediately yields ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/08e47f7cb7315bf22ae83180335e6915035f0d8b).

This has a simple probabilistic interpretation. Consider a random walk on the integer line, starting at 0. Let -1 be a "trap" state, such that if the walker arrives at -1, it will remain there. The walker can arrive at the trap state at times 1, 3, 5, 7..., and the number of ways the walker can arrive at the trap state at time ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/3e87e8e0ad79ff1c62b35c9ef502fd75c469b79b) is ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/1a0887b56787ba96e79de2b9f5c6ff30aabad1c6). Since the 1D random walk is recurrent, the probability that the walker eventually arrives at -1 is ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/55eeefb03186513a58165dfb0fb5d4140e805d7b).

Applications in combinatorics[[edit source](/w/index. php? title=Catalan_number&action=edit&section=2 "Edit section's source code: Applications in combinatorics")]
-----------------------------------------------------------------------------------------------------------------------------------------------------------------

There are many counting problems in [combinatorics](/wiki/Combinatorics "Combinatorics") whose solution is given by the Catalan numbers. The book _Enumerative Combinatorics: Volume 2_ by combinatorialist [Richard P. Stanley](/wiki/Richard_P._Stanley "Richard P. Stanley") contains a set of exercises which describe 66 different interpretations of the Catalan numbers. Following are some examples, with illustrations of the cases _C_3 = 5 and _C_4 = 14.

![](http://upload.wikimedia.org/wikipedia/commons/thumb/0/01/Dyck_lattice_D4.svg/220px-Dyck_lattice_D4.svg.png)Lattice of the 14 Dyck words of length 8 – _(_ and _)_ interpreted as _up_ and _down_

*   _C__n_ is the number of [Dyck words](/wiki/Dyck_word "Dyck word") [[2]] ( #cite_note -2) of length 2_n_. A Dyck word is a [string](/wiki/String_(computer_science) "String (computer science)") consisting of _n_ X's and _n_ Y's such that no initial segment of the string has more Y's than X's. For example, the following are the Dyck words up to length 6:

XY

XXYY     XYXY

XXXYYY     XYXXYY     XYXYXY     XXYYXY     XXYXYY

*   Re-interpreting the symbol X as an open [parenthesis](/wiki/Bracket #Parentheses "Bracket") and Y as a close parenthesis, _C__n_ counts the number of expressions containing _n_ pairs of parentheses which are correctly matched:

((()))     (()())     (())()     ()(())     ()()()

*   _C__n_ is the number of different ways _n_ + 1 factors can be completely [parenthesized](/wiki/Bracket "Bracket") (or the number of ways of [associating](/wiki/Associativity "Associativity") _n_ applications of a [binary operator](/wiki/Binary_operator "Binary operator"), as in the [matrix chain multiplication](/wiki/Matrix_chain_multiplication "Matrix chain multiplication") problem). For _n_ = 3, for example, we have the following five different parenthesizations of four factors:

((ab) c) d     (a (bc)) d     (ab)(cd)     a ((bc) d)     a (b (cd))

*   Successive applications of a binary operator can be represented in terms of a [full binary tree](/wiki/Binary_tree #Types_of_binary_trees "Binary tree"), by labeling each leaf _a, b, c, d_. It follows that _C__n_ is the number of full binary [trees](/wiki/Tree_(graph_theory) "Tree (graph theory)") with _n_ + 1 leaves, or, equivalently, with a total of _n_ internal nodes:

![](http://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/Catalan_4_leaves_binary_tree_example.svg/650px-Catalan_4_leaves_binary_tree_example.svg.png)

![](http://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Tamari_lattice%2C_trees.svg/220px-Tamari_lattice%2C_trees.svg.png)The [associahedron](/wiki/Associahedron "Associahedron") of order 4 with the C4=14 full binary trees with 5 leaves

*   _C__n_ is the number of non-isomorphic [ordered (or plane) trees](/wiki/Tree_(graph_theory) #Plane_tree "Tree (graph theory)") with _n_ + 1 vertices. [[3]] ( #cite_note -3) See [encoding general trees as binary trees](/wiki/Binary_tree #Encoding_general_trees_as_binary_trees "Binary tree"). For example, _C__n_ is the number of possible [parse trees](/wiki/Parse_tree "Parse tree") for a sentence (assuming binary branching), in natural language processing.
*   _C__n_ is the number of monotonic [lattice paths](/wiki/Lattice_path "Lattice path") along the edges of a grid with _n_ × _n_ square cells, which do not pass above the diagonal. A monotonic path is one which starts in the lower left corner, finishes in the upper right corner, and consists entirely of edges pointing rightwards or upwards. Counting such paths is equivalent to counting Dyck words: X stands for "move right" and Y stands for "move up".

The following diagrams show the case _n_ = 4:

![](http://upload.wikimedia.org/wikipedia/commons/thumb/f/f4/Catalan_number_4x4_grid_example.svg/450px-Catalan_number_4x4_grid_example.svg.png)

This can be represented by listing the Catalan elements by column height: [[4]] ( #cite_note -4)

![](http://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tamari_lattice%2C_hexagons.svg/220px-Tamari_lattice%2C_hexagons.svg.png)The dark triangle is the root node, the light triangles correspond to internal nodes of the binary trees, and the green bars are the leaves.

[0,0,0,0] [0,0,0,1] [0,0,0,2] [0,0,1,1]

[0,1,1,1] [0,0,1,2] [0,0,0,3] [0,1,1,2] [0,0,2,2] [0,0,1,3]

[0,0,2,3] [0,1,1,3] [0,1,2,2] [0,1,2,3]

*   A [convex polygon](/wiki/Convex_polygon "Convex polygon") with _n_ + 2 sides can be cut into [triangles](/wiki/Triangle "Triangle") by connecting vertices with non-crossing [line segments](/wiki/Line_segment "Line segment") (a form of [polygon triangulation](/wiki/Polygon_triangulation "Polygon triangulation")). The number of triangles formed is _n_ and the number of different ways that this can be achieved is _C__n_. The following hexagons illustrate the case _n_ = 4:

![](http://upload.wikimedia.org/wikipedia/commons/thumb/a/a8/Catalan-Hexagons-example.svg/400px-Catalan-Hexagons-example.svg.png)

*   _C__n_ is the number of [stack](/wiki/Stack_(data_structure) "Stack (data structure)")-sortable [permutations](/wiki/Permutation "Permutation") of {1, ..., _n_}. A permutation _w_ is called [stack-sortable](/wiki/Stack-sortable_permutation "Stack-sortable permutation") if _S_(_w_) = (1, ..., _n_), where _S_(_w_) is defined recursively as follows: write _w_ = _unv_ where _n_ is the largest element in _w_ and _u_ and _v_ are shorter sequences, and set _S_(_w_) = _S_(_u_)_S_(_v_)_n_, with _S_ being the identity for one-element sequences.
*   _C__n_ is the number of permutations of {1, ..., _n_} that avoid the [permutation pattern](/wiki/Permutation_pattern "Permutation pattern") 123 (or, alternatively, any of the other patterns of length 3); that is, the number of permutations with no three-term increasing subsequence. For _n_ = 3, these permutations are 132, 213, 231, 312 and 321. For _n_ = 4, they are 1432, 2143, 2413, 2431, 3142, 3214, 3241, 3412, 3421, 4132, 4213, 4231, 4312 and 4321.
*   _C__n_ is the number of [noncrossing partitions](/wiki/Noncrossing_partition "Noncrossing partition") of the set {1, ..., _n_}. [_A fortiori_](/wiki/A_fortiori_argument "A fortiori argument"), _C__n_ never exceeds the _n_th [Bell number](/wiki/Bell_number "Bell number"). _C__n_ is also the number of noncrossing partitions of the set {1, ..., 2_n_} in which every block is of size 2.
*   _C__n_ is the number of ways to tile a stairstep shape of height _n_ with _n_ rectangles. Cutting across the anti-diagonal and looking at only the edges gives full binary trees. The following figure illustrates the case _n_ = 4:

![](http://upload.wikimedia.org/wikipedia/commons/thumb/6/63/Catalan_stairsteps_4.svg/400px-Catalan_stairsteps_4.svg.png)

*   _C__n_ is the number of ways to form a "mountain range" with _n_ upstrokes and _n_ downstrokes that all stay above a horizontal line. The mountain range interpretation is that the mountains will never go below the horizon.

<table><caption>Mountain Ranges</caption><tbody><tr><td><img class="" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/9af3cb17ab89fdb966ba03d164faba4cb65cd50f"></td><td>*</td><td>1 way</td></tr><tr><td><img class="" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/55d096081e2a20f6ca103fdebbd70bf687eb8f3c"></td><td>/\</td><td>1 way</td></tr><tr><td><img class="" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/369c80e16851b43b7613fb8af7841f2f57235d6e"></td><td>0000000/\<br>/\/\, 0/00\</td><td>2 ways</td></tr><tr><td><img class="" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/2059d374aa5c2b3f98d34b350c7cd98f8d28d85d"></td><td>0000000000000000000000000000000000/\<br>00000000000/\0000/\000000/\/\0000/00\<br>/\/\/\, 0/\/00\, 0/00\/\, 0/0000\, 0/0000\</td><td>5 ways</td></tr></tbody></table>

*   _C__n_ is the number of [standard Young tableaux](/wiki/Young_tableau #Tableaux "Young tableau") whose diagram is a 2-by-_n_ rectangle. In other words, it is the number of ways the numbers 1, 2, ..., 2_n_ can be arranged in a 2-by-_n_ rectangle so that each row and each column is increasing. As such, the formula can be derived as a special case of the [hook-length formula](/wiki/Young_tableau #Dimension_of_a_representation "Young tableau").

```
123   124   125   134   135
456   356   346   256   246
```

*   ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0301812adb392070d834ca2df4ed97f1cf132f33) is the number of length n sequences that start with ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/92d98b82a3778f043108d4e20960a9193df57cbf), and can increase by either ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/2aae8864a3c1fec9585261791a809ddec1489950) or ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/92d98b82a3778f043108d4e20960a9193df57cbf), or decrease by any number (to at least ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/92d98b82a3778f043108d4e20960a9193df57cbf)). For ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d928ec15aeef83aade867992ee473933adb6139d) these are ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/950f60d50d828ea8b24086174260d766b511803b). From a Dyck path, start a counter at _0_. An X increases the counter by _1_ and a Y decreases it by _1_. Record the values at only the X's. Compared to the similar representation of the [Bell numbers](/wiki/Bell_number #Rhyme_schemes "Bell number"), only ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/7c088ee44f61cbd18f31bc881116e610a70cea9b) is missing.

Proof of the formula[[edit source](/w/index. php? title=Catalan_number&action=edit&section=3 "Edit section's source code: Proof of the formula")]
-----------------------------------------------------------------------------------------------------------------------------------------------

There are several ways of explaining why the formula

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/7d16a3d3295935d6095a14c7b39b7d2171ff9e7a)

solves the combinatorial problems listed above. The first proof below uses a [generating function](/wiki/Generating_function "Generating function"). The other proofs are examples of [bijective proofs](/wiki/Bijective_proof "Bijective proof"); they involve literally counting a collection of some kind of object to arrive at the correct formula.

### First proof[[edit source](/w/index. php? title=Catalan_number&action=edit&section=4 "Edit section's source code: First proof")]

We first observe that all of the combinatorial problems listed above satisfy [Segner's](/wiki/Johann_Andreas_Segner "Johann Andreas Segner") [[5]] ( #cite_note -5) [recurrence relation](/wiki/Recurrence_relation "Recurrence relation")

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/5c513933c476c27b9c623605771cacedd13926e0)

For example, every Dyck word _w_ of length ≥ 2 can be written in a unique way in the form

_w_ = X_w_1Y_w_2

with (possibly empty) Dyck words _w_1 and _w_2.

The [generating function](/wiki/Generating_function "Generating function") for the Catalan numbers is defined by

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/ea6d661d30e21291fa0a6b238a073f440a21242b)

The recurrence relation given above can then be summarized in generating function form by the relation

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/ca09097d8da576e76997f524dfadffb6d56ea11b)

in other words, this equation follows from the recurrence relation by expanding both sides into [power series](/wiki/Power_series "Power series"). On the one hand, the recurrence relation uniquely determines the Catalan numbers; on the other hand, interpreting _xc_2 − _c_ + 1 = 0 as a [quadratic equation](/wiki/Quadratic_equation "Quadratic equation") of _c_ and using the [quadratic formula](/wiki/Quadratic_formula "Quadratic formula"), the generating function relation can be algebraically solved to yield two solution possibilities

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a29c2d1d79b96af25f921f329e7757399dae6e73)  or  ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/665410f9172a360da5201961306a8df7410cd630).

From the two possibilities, the second must be chosen because only the choice of the second gives

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/858698d08ac0ce77ba065ebe5db4baae883e5b48).

The square root term can be expanded as a power series using the [binomial series](/wiki/Binomial_series "Binomial series")

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/985c6e7a1725e61b4347b4bd29050999fbab8c87)

as we know

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/27a947f8c3de860b6891629adc5ff35b59456667)

and

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/9336cbc48614e06e21c99bbd5e25069ee991b7a2)

Setting _y_ = −4_x_ gives

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/f7b981071a5b5aa7b33fc1f905919b96f47ed9d5)

and substituting this power series into the expression for _c_(_x_), the expansion simplifies to

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/fe68cb029fbc9e2a0e28b66c32d3510c130b4a35).

Let ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/5e5acf46ac6672769b409fe2ea2e1a610f97259e), so that

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/c800929084c7e05ffb85100811d1779c66fb8793)

and because ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d89804ec8bd216f404a13f73fc8d89e621291aa4) (see [proof of recurrence](#Properties))

we have

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d3a7ae38a89f59179a25267b1f90f27c9f5da71f)

### Second proof[[edit source](/w/index. php? title=Catalan_number&action=edit&section=5 "Edit section's source code: Second proof")]

[![](http://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Catalan_number-path_reflection.svg/110px-Catalan_number-path_reflection.svg.png)](/wiki/File: Catalan_number-path_reflection. svg) Figure 1. The invalid portion of the path (dotted red) is flipped (solid red). Bad paths (after the flip) reach (_n_ – 1, _n_ + 1) instead of (_n_,_n_).

We count the number of paths which start and end on the diagonal of a _n_ × _n_ grid. All such paths have _n_ right and _n_ up steps. Since we can choose which of the 2_n_ steps are up or right, there are in total ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0167c547c80cc10d7b031b84bf720e32c4b81c3c) monotonic paths of this type. A _bad_ path crosses the main diagonal and touches the next higher diagonal (red in the illustration).

The part of the path after the higher diagonal is then flipped about that diagonal, as illustrated with the red dotted line. This swaps all the right steps to up steps and vice versa. In the section of the path that is not reflected, there is one more up step than right steps, so therefore the remaining section of the bad path has one more right step than up steps. When this portion of the path is reflected, it will have one more up step than right steps.

Since there are still 2_n_ steps, there are now _n_ + 1 up steps and _n_ − 1 right steps. So, instead of reaching (_n_,_n_), all bad paths after reflection end at (_n_ − 1, _n_ + 1). Because every monotonic path in the (_n_ − 1) × (_n_ + 1) grid meets the higher diagonal, and because the reflection process is reversible, the reflection is therefore a bijection between bad paths in the original grid and monotonic paths in the new grid.

The number of bad paths is therefore:

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/768c8684e7005930fd31ce02fd5a7d8ca7765caa)

and the number of Catalan paths (i.e. good paths) is obtained by removing the number of bad paths from the total number of monotonic paths of the original grid,

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/5bbe1b2abf2e2572d986e3be1188707b6a01bfdb)

In terms of Dyck words, we start with a (non-Dyck) sequence of _n_ X's and _n_ Y's and interchange all X's and Y's after the first Y that violates the Dyck condition. After this Y, note that there is exactly one more Y than there are Xs.

### Third proof[[edit source](/w/index. php? title=Catalan_number&action=edit&section=6 "Edit section's source code: Third proof")]

This bijective proof provides a natural explanation for the term _n_ + 1 appearing in the denominator of the formula for _C__n_. A generalized version of this proof can be found in a paper of Rukavicka Josef (2011). [[6]] ( #cite_note -6)

[![](http://upload.wikimedia.org/wikipedia/commons/a/aa/Catalan_number_exceedance_example.png)](/wiki/File: Catalan_number_exceedance_example. png) Figure 2. A path with exceedance 5.

Given a monotonic path, the **exceedance** of the path is defined to be the number of **vertical** edges above the diagonal. For example, in Figure 2, the edges above the diagonal are marked in red, so the exceedance of this path is 5.

Given a monotonic path whose exceedance is not zero, we apply the following algorithm to construct a new path whose exceedance is _1_ less than the one we started with.

*   Starting from the bottom left, follow the path until it first travels above the diagonal.
*   Continue to follow the path until it _touches_ the diagonal again. Denote by _X_ the first such edge that is reached.
*   Swap the portion of the path occurring before _X_ with the portion occurring after _X_.

In Figure 3, the black dot indicates the point where the path first crosses the diagonal. The black edge is _X_, and we place the last lattice point of the red portion in the top-right corner, and the first lattice point of the green portion in the bottom-left corner, and place X accordingly, to make a new path, shown in the second diagram.

![](http://upload.wikimedia.org/wikipedia/commons/1/13/Catalan_number_swapping_example.png)Figure 3. The green and red portions are being exchanged.

The exceedance has dropped from _3_ to _2_. In fact, the algorithm causes the exceedance to decrease by _1_ for any path that we feed it, because the first vertical step starting on the diagonal (at the point marked with a black dot) is the only vertical edge that passes from above the diagonal to below it after applying the algorithm - all the other vertical edges stay on the same side of the diagonal.

![](http://upload.wikimedia.org/wikipedia/commons/6/65/Catalan_number_algorithm_table.png)Figure 4. All monotonic paths in a 3×3 grid, illustrating the exceedance-decreasing algorithm.

It can be seen that this process is _reversible_: given any path _P_ whose exceedance is less than _n_, there is exactly one path which yields _P_ when the algorithm is applied to it. Indeed, the (black) edge _X_, which originally was the first horizontal step ending on the diagonal, has become the _last_ horizontal step _starting_ on the diagonal. Alternatively, reverse the original algorithm to look for the first edge that passes _below_ the diagonal.

This implies that the number of paths of exceedance _n_ is equal to the number of paths of exceedance _n_ − 1, which is equal to the number of paths of exceedance _n_ − 2, and so on, down to zero. In other words, we have split up the set of _all_ monotonic paths into _n_ + 1 equally sized classes, corresponding to the possible exceedances between 0 and _n_. Since there are ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/4a334a538420fdef7c072b34de7b03826201ade1) monotonic paths, we obtain the desired formula ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/ce7f0549d2f863ad02ee5ff83d728c5d0a20d344)

Figure 4 illustrates the situation for _n_ = 3. Each of the 20 possible monotonic paths appears somewhere in the table. The first column shows all paths of exceedance three, which lie entirely above the diagonal. The columns to the right show the result of successive applications of the algorithm, with the exceedance decreasing one unit at a time. There are five rows, that is, _C_3 = 5, and the last column displays all paths no higher than the diagonal.

Using Dyck words, start with a sequence from ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/cf0366c97c933d232b2c7cbe15a49fa95bbe151a). Let ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/9056678640037c04f016c0a8985fd04956c25caf) be the first X that brings an initial subsequence to equality, and configure the sequence as ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/ce210c0e8b3927b1fea2761cfc123af5ad11ff95). The new sequence is ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/1484d0d9afdc73ffdd50eb406e5b28d03d0bacf0).

### Fourth proof[[edit source](/w/index. php? title=Catalan_number&action=edit&section=7 "Edit section's source code: Fourth proof")]

This proof uses the triangulation definition of Catalan numbers to establish a relation between _Cn_ and _C__n_+1.

Given a polygon _P_ with _n_ + 2 sides and a triangulation, mark one of its sides as the base, and also orient one of its 2_n_ + 1 total edges. There are (4_n_ + 2)_C__n_ such marked triangulations for a given base.

Given a polygon _Q_ with _n_ + 3 sides and a (different) triangulation, again mark one of its sides as the base. Mark one of the sides other than the base side (and not an inner triangle edge). There are (_n_ + 2)_C__n_ + 1 such marked triangulations for a given base.

There is a simple bijection between these two marked triangulations: We can either collapse the triangle in _Q_ whose side is marked (in two ways, and subtract the two that cannot collapse the base), or, in reverse, expand the oriented edge in _P_ to a triangle and mark its new side.

Thus

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/9a21a28d3d9607b7793bd479b7369d5c573dff89).

Write ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/6582a98988bdd21afb8cb73501e60570ab5ecb58)

Because

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/8e78fec0010ed12e765d5fe3dba51429608e78ab)

we have

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/6d841355c8f655abf5ac494d3344b1223bb57410)

Applying the recursion with ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/8f16d74f9b1af01a229f5b576167e4f1d7969c83) gives the result.

### Fifth proof[[edit source](/w/index. php? title=Catalan_number&action=edit&section=8 "Edit section's source code: Fifth proof")]

This proof is based on the [Dyck words](/wiki/Dyck_language "Dyck language") interpretation of the Catalan numbers, so ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0301812adb392070d834ca2df4ed97f1cf132f33) is the number of ways to correctly match n pairs of brackets. We denote a (possibly empty) correct string with c and its inverse with c'. Since any c can be uniquely decomposed into ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/578f2acb84e205afae8934e1fd643006e735c4c5), summing over the possible lengths of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/77b7dc6d279091d354e0b90889b463bfa7eb7247) immediately gives the recursive definition

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/b2d5810373bb41ff03a3ca0ac50207ce3a713289).

Let b be a balanced string of length 2n, i.e. b contains an equal number of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0ccb001e791b6a7340825e0e0b1a60e4e03f3d78) and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/775c8f99fbc2db4ef20dd618a468f110bae7bd76), so ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/867d14a59461dc2cba8ee84c6ecc0ca5498779c9). A balanced string can also be uniquely decomposed into either ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/b8d70646af49d594b1bafc0e4fb90bf86db65d56) or ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/c52f5799661566e3ecb5a4d7e4d61b6210181e04), so

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d7cda32444e9e98912b338f1e9f5142729bb1657)

Any incorrect (non-Catalan) balanced string starts with ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/c44b574f25c1bcc536c3256db0f6c196660b739a), and the remaining string has one more ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0ccb001e791b6a7340825e0e0b1a60e4e03f3d78) than ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/775c8f99fbc2db4ef20dd618a468f110bae7bd76), so

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/b21d022ef2eadffae042419b66913a4a65221c0b)

Also, from the definitions, we have:

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/3f14162c81773b35f93d7ff096a362cc5c48e7ab)

Therefore, as this is true for all n,

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/229208f6bb553face03ab9d37f53297796bb997d)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/2bde23e7d018661a3a01c506bcdb6c5e80c72dd8)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/6104d14f1e1e7aae97911cc478c345d284718caf)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0541081f43541ca5963c8793e62110911f3ba88a)

### Sixth proof[[edit source](/w/index. php? title=Catalan_number&action=edit&section=9 "Edit section's source code: Sixth proof")]

This proof is based on the [Dyck words](/wiki/Dyck_language "Dyck language") interpretation of the Catalan numbers and uses the [cycle lemma](/wiki/Cycle_lemma #Proof_by_the_cycle_lemma "Cycle lemma") of Dvoretzky and Motzkin. [[7]] ( #cite_note -7) [[8]] ( #cite_note -8)

We call a sequence of X's and Y's _dominating_ if, reading from left to right, the number of X's is always strictly greater than the number of Y's. The cycle lemma [[9]] ( #cite_note -9) states that any sequence of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0a07d98bb302f3856cbabc47b2b9016692e3f7bc) X's and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) Y's, where ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/637039c4a193f33fee72ebfeb6cb003593696160), has precisely ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/767c4b0c3cbd063f836169c2db77f5ffd833d136) dominating [circular shifts](/wiki/Circular_shift "Circular shift"). To see this, arrange the given sequence of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/88528fefcfac1b22d2df9b71d0f4fc9e758f65ad) X's and Y's in a circle. Repeatedly removing XY pairs leaves exactly ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/767c4b0c3cbd063f836169c2db77f5ffd833d136) X's. Each of these X's was the start of a dominating circular shift before anything was removed. For example, consider ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/f3e468e7db7128a19f68d9ec5125a048c4ac1e29). This sequence is dominating, but none of its circular shifts ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/720e1f8b892274619b250dc2dcc9acbe10f45688), ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d20a7be65731615e19deed3f25aee8f9c250cac1), ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/b53d7ad2eeb52b6deb648871064eb337190ba7b1) and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/60ea1f0cb6880487c50a4b82d63a3052a7b3882d) are.

A string is a Dyck word of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) X's and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) Y's if and only if prepending an X to the Dyck word gives a dominating sequence with ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/2a135e65a42f2d73cccbfc4569523996ca0036f1) X's and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) Y's, so we can count the former by instead counting the latter. In particular, when ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/6eb5d12f9c732b88f2f40e08ad52858e637e02f9), there is exactly one dominating circular shift. There are ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/98b8a9bf34c5e27f18397ffe96e3579e124d0a5e) sequences with exactly ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/2a135e65a42f2d73cccbfc4569523996ca0036f1) X's and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) Y's. For each of these, only one of the ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/8ca410f731fe4c7c444330343afb1d1850eadaea) circular shifts is dominating. Therefore there are ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/55bf3392660f75d45c0dc35eb2a24394760fdaaa) distinct sequences of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/2a135e65a42f2d73cccbfc4569523996ca0036f1) X's and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) Y's that are dominating, each of which corresponds to exactly one Dyck word.

Hankel matrix[[edit source](/w/index. php? title=Catalan_number&action=edit&section=10 "Edit section's source code: Hankel matrix")]
----------------------------------------------------------------------------------------------------------------------------------

The _n_×_n_ [Hankel matrix](/wiki/Hankel_matrix "Hankel matrix") whose (_i_, _j_) entry is the Catalan number _C__i_+_j_−2 has [determinant](/wiki/Determinant "Determinant") 1, regardless of the value of _n_. For example, for _n_ = 4 we have

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/6f0b68f4925b66c114fd80870a912001b83cf19c)

Moreover, if the indexing is "shifted" so that the (_i_, _j_) entry is filled with the Catalan number _C__i_+_j_−1 then the determinant is still 1, regardless of the value of _n_. For example, for _n_ = 4 we have

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/bd5867813147cc389953fb4e77b3d333bdc2c650)

Taken together, these two conditions uniquely define the Catalan numbers.

Another feature unique to the Catalan–Hankel matrix is the determinant of the _n_×_n_ submatrix starting at _2_ has determinant _n_ + 1.

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/cde444dd3166c22d54768126fd9a20ca49dae3d3)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/e30d729570ff5031b76b867dabe7f03ba9f441ef)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/4eb04717ec50ad6bc6c9936b0f43b75694b08678)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/c0d07390a3d674718996884bf7f93c44c61c8d9a)

et cetera.

History[[edit source](/w/index. php? title=Catalan_number&action=edit&section=11 "Edit section's source code: History")]
----------------------------------------------------------------------------------------------------------------------

![](http://upload.wikimedia.org/wikipedia/commons/thumb/4/47/Mingantu%27s_Catalan_numbers.JPG/400px-Mingantu%27s_Catalan_numbers.JPG)Catalan numbers in Mingantu's book _The Quick Method for Obtaining the Precise Ratio of Division of a Circle_ volume III

The Catalan sequence was described in the 18th century by [Leonhard Euler](/wiki/Leonhard_Euler "Leonhard Euler"), who was interested in the number of different ways of dividing a polygon into triangles. The sequence is named after [Eugène Charles Catalan](/wiki/Eug%C3%A8ne_Charles_Catalan "Eugène Charles Catalan"), who discovered the connection to parenthesized expressions during his exploration of the [Towers of Hanoi](/wiki/Towers_of_Hanoi "Towers of Hanoi") puzzle. The reflection counting trick (second proof) for Dyck words was found by [Désiré André](/wiki/D%C3%A9sir%C3%A9_Andr%C3%A9 "Désiré André") in 1887.

The name “Catalan numbers” originated from [John Riordan](/wiki/John_Riordan_(mathematician) "John Riordan (mathematician)"). [[10]] ( #cite_note -10)

In 1988, it came to light that the Catalan number sequence had been used in [China](/wiki/China "China") by the Mongolian mathematician [Mingantu](/wiki/Mingantu "Mingantu") by 1730. [[11]] ( #cite_note -11) [[12]] ( #cite_note -12) That is when he started to write his book _Ge Yuan Mi Lu Jie Fa_ _[The Quick Method for Obtaining the Precise Ratio of Division of a Circle]_, which was completed by his student Chen Jixin in 1774 but published sixty years later. Peter J. Larcombe (1999) sketched some of the features of the work of Mingantu, including the stimulus of Pierre Jartoux, who brought three infinite series to China early in the 1700s.

For instance, Ming used the Catalan sequence to express series expansions of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d85ef1a11d31d6c5f2363ab21b43c910e012a9bb) and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/23ab693883740003dd5c2d845aca5d6c02436759) in terms of ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/95a2a215bb78a456fe5662229c73775521b95299).

Generalizations[[edit source](/w/index. php? title=Catalan_number&action=edit&section=12 "Edit section's source code: Generalizations")]
--------------------------------------------------------------------------------------------------------------------------------------

The Catalan numbers can be interpreted as a special case of the [Bertrand's ballot theorem](/wiki/Bertrand%27s_ballot_theorem "Bertrand's ballot theorem"). Specifically, ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0301812adb392070d834ca2df4ed97f1cf132f33) is the number of ways for a candidate A with _n+1_ votes to lead candidate B with _n_ votes.

The two-parameter sequence of non-negative integers ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/81156c73cef5012ad490dd40bd42590cc1f81cb6) is a generalization of the Catalan numbers. These are named **super-Catalan numbers**, per [Ira Gessel](/wiki/Ira_Gessel "Ira Gessel"). These should not confused with the [Schröder–Hipparchus numbers](/wiki/Schr%C3%B6der%E2%80%93Hipparchus_number "Schröder–Hipparchus number"), which sometimes are also called super-Catalan numbers.

For ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/b6100c5ebd48c6fd848709f2be624465203eb173), this is just two times the ordinary Catalan numbers, and for ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/69c9d8e54796e7de7d4738510cc10bc3fc55d48e), the numbers have an easy combinatorial description. However, other combinatorial descriptions are only known [[13]] ( #cite_note -Chen2012-13) for ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/06c0747512b872429cc2d8c31924471559da22d6) and ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/295b4bf1de7cd3500e740e0f4f0635db22d87b42), [[14]] ( #cite_note -14) and it is an open problem to find a general combinatorial interpretation.

[Sergey Fomin](/wiki/Sergey_Fomin "Sergey Fomin") and Nathan Reading have given a generalized Catalan number associated to any finite crystallographic [Coxeter group](/wiki/Coxeter_group "Coxeter group"), namely the number of fully commutative elements of the group; in terms of the associated [root system](/wiki/Root_system "Root system"), it is the number of anti-chains (or order ideals) in the poset of positive roots. The classical Catalan number ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/0301812adb392070d834ca2df4ed97f1cf132f33) corresponds to the root system of type ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/730f6906700685b6d52f3958b1c2ae659d2d97d2). The classical recurrence relation generalizes: the Catalan number of a Coxeter diagram is equal to the sum of the Catalan numbers of all its maximal proper sub-diagrams. [[15]] ( #cite_note -15)

The Catalan numbers are a solution of a version of the [Hausdorff moment problem](/wiki/Hausdorff_moment_problem "Hausdorff moment problem"). [[16]] ( #cite_note -16)

Catalan _k_-fold convolution[[edit source](/w/index. php? title=Catalan_number&action=edit&section=13 "Edit section's source code: Catalan k-fold convolution")]
--------------------------------------------------------------------------------------------------------------------------------------------------------------

The Catalan _k_-fold convolution is: [[17]] ( #cite_note -17)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/45120dc51e2755ca8b30b3ae9e3e6c8717b268f1)

See also[[edit source](/w/index. php? title=Catalan_number&action=edit&section=14 "Edit section's source code: See also")]
------------------------------------------------------------------------------------------------------------------------

*   [![](http://upload.wikimedia.org/wikipedia/commons/thumb/3/3e/Nuvola_apps_edu_mathematics_blue-p.svg/28px-Nuvola_apps_edu_mathematics_blue-p.svg.png)](/wiki/File: Nuvola_apps_edu_mathematics_blue-p.svg)[Mathematics portal](/wiki/Portal: Mathematics "Portal: Mathematics")

*   [Associahedron](/wiki/Associahedron "Associahedron")
*   [Bertrand's ballot theorem](/wiki/Bertrand%27s_ballot_theorem "Bertrand's ballot theorem")
*   [Binomial transform](/wiki/Binomial_transform "Binomial transform")
*   [Catalan's triangle](/wiki/Catalan%27s_triangle "Catalan's triangle")
*   [Catalan–Mersenne number](/wiki/Catalan%E2%80%93Mersenne_number "Catalan–Mersenne number")
*   [Fuss–Catalan number](/wiki/Fuss%E2%80%93Catalan_number "Fuss–Catalan number")
*   [List of factorial and binomial topics](/wiki/List_of_factorial_and_binomial_topics "List of factorial and binomial topics")
*   [Lobb numbers](/wiki/Lobb_numbers "Lobb numbers")
*   [Motzkin number](/wiki/Motzkin_number "Motzkin number")
*   [Narayana number](/wiki/Narayana_number "Narayana number")
*   [Schröder–Hipparchus number](/wiki/Schr%C3%B6der%E2%80%93Hipparchus_number "Schröder–Hipparchus number")
*   [Semiorder](/wiki/Semiorder "Semiorder")
*   [Tamari lattice](/wiki/Tamari_lattice "Tamari lattice")
*   [Wedderburn–Etherington number](/wiki/Wedderburn%E2%80%93Etherington_number "Wedderburn–Etherington number")
*   [Wigner's semicircle law](/wiki/Wigner%27s_semicircle_law "Wigner's semicircle law")

Notes[[edit source](/w/index. php? title=Catalan_number&action=edit&section=15 "Edit section's source code: Notes")]
------------------------------------------------------------------------------------------------------------------

1.  **[^]( #cite_ref -1 "Jump up")**
2.  **[^]( #cite_ref -2 "Jump up")** [Dyck paths](https://www.findstat.org/CollectionsDatabase/Cc0005/)
3.  **[^]( #cite_ref -3 "Jump up")** Stanley p.221 example (e)
4.  **[^]( #cite_ref -4 "Jump up")** Črepinšek, Matej; Mernik, Luka (2009). ["An efficient representation for solving Catalan number related problems"](http://www.ijpam.eu/contents/2009-56-4/11/11.pdf) (PDF). _International Journal of Pure and Applied Mathematics_. **56** (4): 589–604.
5.  **[^]( #cite_ref -5 "Jump up")** A. de Segner, Enumeratio modorum, quibus figurae planae rectilineae per diagonales dividuntur in triangula. _Novi commentarii academiae scientiarum Petropolitanae_ **7** (1758/59) 203–209.
6.  **[^]( #cite_ref -6 "Jump up")** Rukavicka Josef (2011), _On Generalized Dyck Paths, Electronic Journal of Combinatorics_ [online](http://www.combinatorics.org/ojs/index.php/eljc/article/view/v18i1p40/pdf)
7.  **[^]( #cite_ref -7 "Jump up")** Dershowitz, Nachum; Zaks, Shmuel (1980), "Enumerations of ordered trees", _Discrete Mathematics_, **31**: 9–28, [doi](/wiki/Doi_(identifier) "Doi (identifier)"): [10.1016/0012-365x(80)90168-5](https://doi.org/10.1016%2F0012-365x%2880%2990168-5), [hdl](/wiki/Hdl_(identifier) "Hdl (identifier)"): [2027/uiuo.ark:/13960/t3kw6z60d](https://hdl.handle.net/2027%2Fuiuo.ark%3A%2F13960%2Ft3kw6z60d)
8.  **[^]( #cite_ref -8 "Jump up")** Dvoretzky, Aryeh; Motzkin, Theodore (1947), "A problem of arrangements", _Duke Mathematical Journal_, **14** (2): 305–313, [doi](/wiki/Doi_(identifier) "Doi (identifier)"): [10.1215/s0012-7094-47-01423-3](https://doi.org/10.1215%2Fs0012-7094-47-01423-3)
9.  **[^]( #cite_ref -9 "Jump up")** Dershowitz, Nachum; Zaks, Shmuel (January 1990). ["The Cycle Lemma and Some Applications"](http://www.cs.tau.ac.il/~nachumd/papers/CL.pdf) (PDF). _European Journal of Combinatorics_. **11** (1): 35–40. [doi](/wiki/Doi_(identifier) "Doi (identifier)"): [10.1016/S0195-6698(13)80053-4](https://doi.org/10.1016%2FS0195-6698%2813%2980053-4).
10.  **[^]( #cite_ref -10 "Jump up")** Stanley, Richard P. (2021). "Enumerative and Algebraic Combinatorics in the 1960's and 1970's". [arXiv](/wiki/ArXiv_(identifier) "ArXiv (identifier)"): [2105.07884](https://arxiv.org/abs/2105.07884) [[math.HO](https://arxiv.org/archive/math.HO)].
11.  **[^]( #cite_ref -11 "Jump up")** Larcombe, Peter J. ["The 18th century Chinese discovery of the Catalan numbers"](https://www.math.ucla.edu/~pak/lectures/Cat/Larcombe-The_18th_century_Chinese_discovery_of_the_Catalan_numbers.pdf) (PDF).
12.  **[^]( #cite_ref -12 "Jump up")** ["Ming Antu, the First Inventor of Catalan Numbers in the World"](http://en.cnki.com.cn/Article_en/CJFDTOTAL-NMGX198802004.htm).
13.  **[^]( #cite_ref -Chen2012_13-0 "Jump up")** Chen, Xin; Wang, Jane (2012). "The super Catalan numbers S (m, m + s) for s ≤ 4". [arXiv](/wiki/ArXiv_(identifier) "ArXiv (identifier)"): [1208.4196](https://arxiv.org/abs/1208.4196) [[math.CO](https://arxiv.org/archive/math.CO)].
14.  **[^]( #cite_ref -14 "Jump up")** Gheorghiciuc, Irina; Orelowitz, Gidon (2020). "Super-Catalan Numbers of the Third and Fourth Kind". [arXiv](/wiki/ArXiv_(identifier) "ArXiv (identifier)"): [2008.00133](https://arxiv.org/abs/2008.00133) [[math.CO](https://arxiv.org/archive/math.CO)].
15.  **[^]( #cite_ref -15 "Jump up")** [Sergey Fomin](/wiki/Sergey_Fomin "Sergey Fomin") and Nathan Reading, "Root systems and generalized associahedra", Geometric combinatorics, IAS/Park City Math. Ser. **13**, [American Mathematical Society](/wiki/American_Mathematical_Society "American Mathematical Society"), Providence, RI, 2007, pp 63–131. [arXiv](/wiki/ArXiv_(identifier) "ArXiv (identifier)"):[math/0505518]( https://arxiv.org/abs/math/0505518 "arxiv: math/0505518")
16.  **[^]( #cite_ref -16 "Jump up")** Choi, Hayoung; Yeh, Yeong-Nan; Yoo, Seonguk (2020), "Catalan-like number sequences and Hausdorff moment sequences", _Discrete Mathematics_, **343** (5): 111808, 11, [arXiv](/wiki/ArXiv_(identifier) "ArXiv (identifier)"): [1809.07523](https://arxiv.org/abs/1809.07523), [doi](/wiki/Doi_(identifier) "Doi (identifier)"): [10.1016/j.disc.2019.111808](https://doi.org/10.1016%2Fj.disc.2019.111808), [MR](/wiki/MR_(identifier) "MR (identifier)") [4052255](https://mathscinet.ams.org/mathscinet-getitem?mr=4052255), [S2CID](/wiki/S2CID_(identifier) "S2CID (identifier)") [214165563](https://api.semanticscholar.org/CorpusID:214165563)
17.  **[^]( #cite_ref -17 "Jump up")** Bowman, D.; Regev, Alon (2014). ["Counting symmetry: classes of dissections of a convex regular polygon"](https://doi.org/10.1016%2Fj.aam.2014.01.004). _Adv. Appl. Math_. **56**: 35–55. [doi](/wiki/Doi_(identifier) "Doi (identifier)"): [10.1016/j.aam.2014.01.004](https://doi.org/10.1016%2Fj.aam.2014.01.004). [S2CID](/wiki/S2CID_(identifier) "S2CID (identifier)") [15430707](https://api.semanticscholar.org/CorpusID:15430707).

References[[edit source](/w/index. php? title=Catalan_number&action=edit&section=16 "Edit section's source code: References")]
----------------------------------------------------------------------------------------------------------------------------

*   Stanley, Richard P. (2015), _Catalan numbers_. Cambridge University Press, [ISBN](/wiki/ISBN_(identifier) "ISBN (identifier)") [978-1-107-42774-7](/wiki/Special: BookSources/978-1-107-42774-7 "Special: BookSources/978-1-107-42774-7").
*   [Conway](/wiki/John_H._Conway "John H. Conway") and [Guy](/wiki/Richard_K._Guy "Richard K. Guy") (1996) _The Book of Numbers_. New York: Copernicus, pp. 96–106.
*   [Gardner, Martin](/wiki/Martin_Gardner "Martin Gardner") (1988), [_Time Travel and Other Mathematical Bewilderments_](https://archive.org/details/timetravelotherm0000gard/page/253), New York: W.H. Freeman and Company, pp. [253–266 (Ch. 20)](https://archive.org/details/timetravelotherm0000gard/page/253), [Bibcode](/wiki/Bibcode_(identifier) "Bibcode (identifier)"): [1988ttom.book.....G](https://ui.adsabs.harvard.edu/abs/1988ttom.book.....G), [ISBN](/wiki/ISBN_(identifier) "ISBN (identifier)") [0-7167-1924-X](/wiki/Special: BookSources/0-7167-1924-X "Special: BookSources/0-7167-1924-X")
*   Koshy, Thomas (2008), [_Catalan Numbers with Applications_](https://www.amazon.com/Thomas-Koshy/e/B001H6NZT4/ref=ntt_athr_dp_pel_1), Oxford University Press, [ISBN](/wiki/ISBN_(identifier) "ISBN (identifier)") [978-0-19-533454-8](/wiki/Special: BookSources/978-0-19-533454-8 "Special: BookSources/978-0-19-533454-8")
*   Koshy, Thomas & Zhenguang Gao (2011) "Some divisibility properties of Catalan numbers", [Mathematical Gazette](/wiki/Mathematical_Gazette "Mathematical Gazette") 95:96–102.
*   Larcombe, P.J. (1999). ["The 18th century Chinese discovery of the Catalan numbers"](https://www.math.ucla.edu/~pak/lectures/Cat/Larcombe-The_18th_century_Chinese_discovery_of_the_Catalan_numbers.pdf) (PDF). _[Mathematical Spectrum](/wiki/Mathematical_Spectrum "Mathematical Spectrum")_. **32**: 5–7.
*   Stanley, Richard P. (1999), [_Enumerative combinatorics. Vol. 2_](http://www-math.mit.edu/~rstan/ec/), Cambridge Studies in Advanced Mathematics, vol. 62, [Cambridge University Press](/wiki/Cambridge_University_Press "Cambridge University Press"), [ISBN](/wiki/ISBN_(identifier) "ISBN (identifier)") [978-0-521-56069-6](/wiki/Special: BookSources/978-0-521-56069-6 "Special: BookSources/978-0-521-56069-6"), [MR](/wiki/MR_(identifier) "MR (identifier)") [1676282](https://mathscinet.ams.org/mathscinet-getitem?mr=1676282)
*   Egecioglu, Omer (2009), [_A Catalan–Hankel Determinant Evaluation_](http://www.cs.ucsb.edu/~omer/DOWNLOADABLE/catalan_hankel09.pdf) (PDF)
*   Gheorghiciuc, Irina; Orelowitz, Gidon (2020), _Super-Catalan Numbers of the Third and Fourth Kind_, [arXiv](/wiki/ArXiv_(identifier) "ArXiv (identifier)"): [2008.00133](https://arxiv.org/abs/2008.00133)

External links[[edit source](/w/index. php? title=Catalan_number&action=edit&section=17 "Edit section's source code: External links")]
------------------------------------------------------------------------------------------------------------------------------------

*   Stanley, Richard P. (1998), [_Catalan addendum to Enumerative Combinatorics, Volume 2_](http://www-math.mit.edu/~rstan/ec/catadd.pdf) (PDF)
*   [Weisstein, Eric W.](/wiki/Eric_W._Weisstein "Eric W. Weisstein") ["Catalan Number"](https://mathworld.wolfram.com/CatalanNumber.html). _[MathWorld](/wiki/MathWorld "MathWorld")_.
*   Davis, Tom: [Catalan numbers](https://web.archive.org/web/20070216101521/http://mathcircle.berkeley.edu/BMC6/pdf0607/catalan.pdf). Still more examples.
*   "Equivalence of Three Catalan Number Interpretations" from The Wolfram Demonstrations Project [[1]] ( http://demonstrations.wolfram.com/EquivalenceOfThreeCatalanNumberInterpretations/ )
*   [![](http://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Wikiversity_logo_2017.svg/16px-Wikiversity_logo_2017.svg.png)](/wiki/File: Wikiversity_logo_2017. svg) Learning materials related to [Partition related number triangles]( https://en.wikiversity.org/wiki/Partition_related_number_triangles "v: Partition related number triangles") at Wikiversity

*   [Achilles](/wiki/Achilles_number "Achilles number")
*   [Power of 2](/wiki/Power_of_two "Power of two")
*   [Power of 3](/wiki/Power_of_three "Power of three")
*   [Power of 10](/wiki/Power_of_10 "Power of 10")
*   [Square](/wiki/Square_number "Square number")
*   [Cube](/wiki/Cube_(algebra) "Cube (algebra)")
*   [Fourth power](/wiki/Fourth_power "Fourth power")
*   [Fifth power](/wiki/Fifth_power_(algebra) "Fifth power (algebra)")
*   [Sixth power](/wiki/Sixth_power "Sixth power")
*   [Seventh power](/wiki/Seventh_power "Seventh power")
*   [Eighth power](/wiki/Eighth_power "Eighth power")
*   [Perfect power](/wiki/Perfect_power "Perfect power")
*   [Powerful](/wiki/Powerful_number "Powerful number")
*   [Prime power](/wiki/Prime_power "Prime power")

<table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><div id="Of_the_form_a_×_2b_±_1">Of the form <i>a</i> × 2<sup><i>b</i></sup> ± 1</div></th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Cullen_number" title="Cullen number">Cullen</a></li><li><a href="/wiki/Double_Mersenne_number" title="Double Mersenne number">Double Mersenne</a></li><li><a href="/wiki/Fermat_number" title="Fermat number">Fermat</a></li><li><a href="/wiki/Mersenne_prime" title="Mersenne prime">Mersenne</a></li><li><a href="/wiki/Proth_number" title="Proth number">Proth</a></li><li><a href="/wiki/Thabit_number" title="Thabit number">Thabit</a></li><li><a href="/wiki/Woodall_number" title="Woodall number">Woodall</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button>Other polynomial numbers</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Hilbert_number" title="Hilbert number">Hilbert</a></li><li><a href="/wiki/Idoneal_number" title="Idoneal number">Idoneal</a></li><li><a href="/wiki/Leyland_number" title="Leyland number">Leyland</a></li><li><a href="/wiki/Loeschian_number" title="Loeschian number">Loeschian</a></li><li><a href="/wiki/Lucky_numbers_of_Euler" title="Lucky numbers of Euler">Lucky numbers of Euler</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Recursion" title="Recursion">Recursively</a> defined numbers</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Fibonacci_sequence" title="Fibonacci sequence">Fibonacci</a></li><li><a href="/wiki/Jacobsthal_number" title="Jacobsthal number">Jacobsthal</a></li><li><a href="/wiki/Leonardo_number" title="Leonardo number">Leonardo</a></li><li><a href="/wiki/Lucas_number" title="Lucas number">Lucas</a></li><li><a href="/wiki/Padovan_sequence" title="Padovan sequence">Padovan</a></li><li><a href="/wiki/Pell_number" title="Pell number">Pell</a></li><li><a href="/wiki/Perrin_number" title="Perrin number">Perrin</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button>Possessing a specific set of other numbers</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Amenable_number" title="Amenable number">Amenable</a></li><li><a href="/wiki/Congruent_number" title="Congruent number">Congruent</a></li><li><a href="/wiki/Kn%C3%B6del_number" title="Knödel number">Knödel</a></li><li><a href="/wiki/Riesel_number" title="Riesel number">Riesel</a></li><li><a href="/wiki/Sierpi%C5%84ski_number" title="Sierpiński number">Sierpiński</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button>Expressible via specific sums</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Nonhypotenuse_number" title="Nonhypotenuse number">Nonhypotenuse</a></li><li><a href="/wiki/Polite_number" title="Polite number">Polite</a></li><li><a href="/wiki/Practical_number" title="Practical number">Practical</a></li><li><a href="/wiki/Primary_pseudoperfect_number" title="Primary pseudoperfect number">Primary pseudoperfect</a></li><li><a href="/wiki/Ulam_number" title="Ulam number">Ulam</a></li><li><a href="/wiki/Wolstenholme_number" title="Wolstenholme number">Wolstenholme</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Figurate_number" title="Figurate number">Figurate numbers</a></th></tr><tr><td colspan="2"><table><tbody><tr><th scope="row"><a href="/wiki/Two-dimensional_space" title="Two-dimensional space">2-dimensional</a></th><td><table><tbody><tr><th scope="row"><a href="/wiki/Centered_polygonal_number" title="Centered polygonal number">centered</a></th><td><ul><li><a href="/wiki/Centered_triangular_number" title="Centered triangular number">Centered triangular</a></li><li><a href="/wiki/Centered_square_number" title="Centered square number">Centered square</a></li><li><a href="/wiki/Centered_pentagonal_number" title="Centered pentagonal number">Centered pentagonal</a></li><li><a href="/wiki/Centered_hexagonal_number" title="Centered hexagonal number">Centered hexagonal</a></li><li><a href="/wiki/Centered_heptagonal_number" title="Centered heptagonal number">Centered heptagonal</a></li><li><a href="/wiki/Centered_octagonal_number" title="Centered octagonal number">Centered octagonal</a></li><li><a href="/wiki/Centered_nonagonal_number" title="Centered nonagonal number">Centered nonagonal</a></li><li><a href="/wiki/Centered_decagonal_number" title="Centered decagonal number">Centered decagonal</a></li><li><a href="/wiki/Star_number" title="Star number">Star</a></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Polygonal_number" title="Polygonal number">non-centered</a></th><td><ul><li><a href="/wiki/Triangular_number" title="Triangular number">Triangular</a></li><li><a href="/wiki/Square_number" title="Square number">Square</a></li><li><a href="/wiki/Square_triangular_number" title="Square triangular number">Square triangular</a></li><li><a href="/wiki/Pentagonal_number" title="Pentagonal number">Pentagonal</a></li><li><a href="/wiki/Hexagonal_number" title="Hexagonal number">Hexagonal</a></li><li><a href="/wiki/Heptagonal_number" title="Heptagonal number">Heptagonal</a></li><li><a href="/wiki/Octagonal_number" title="Octagonal number">Octagonal</a></li><li><a href="/wiki/Nonagonal_number" title="Nonagonal number">Nonagonal</a></li><li><a href="/wiki/Decagonal_number" title="Decagonal number">Decagonal</a></li><li><a href="/wiki/Dodecagonal_number" title="Dodecagonal number">Dodecagonal</a></li></ul></td></tr></tbody></table></td></tr><tr><th scope="row"><a href="/wiki/Three-dimensional_space" title="Three-dimensional space">3-dimensional</a></th><td><table><tbody><tr><th scope="row"><a href="/wiki/Centered_polyhedral_number" title="Centered polyhedral number">centered</a></th><td><ul><li><a href="/wiki/Centered_tetrahedral_number" title="Centered tetrahedral number">Centered tetrahedral</a></li><li><a href="/wiki/Centered_cube_number" title="Centered cube number">Centered cube</a></li><li><a href="/wiki/Centered_octahedral_number" title="Centered octahedral number">Centered octahedral</a></li><li><a href="/wiki/Centered_dodecahedral_number" title="Centered dodecahedral number">Centered dodecahedral</a></li><li><a href="/wiki/Centered_icosahedral_number" title="Centered icosahedral number">Centered icosahedral</a></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Polyhedral_number" title="Polyhedral number">non-centered</a></th><td><ul><li><a href="/wiki/Tetrahedral_number" title="Tetrahedral number">Tetrahedral</a></li><li><a href="/wiki/Cube_(algebra)" title="Cube (algebra)">Cubic</a></li><li><a href="/wiki/Octahedral_number" title="Octahedral number">Octahedral</a></li><li><a href="/wiki/Dodecahedral_number" title="Dodecahedral number">Dodecahedral</a></li><li><a href="/wiki/Icosahedral_number" title="Icosahedral number">Icosahedral</a></li><li><a href="/wiki/Stella_octangula_number" title="Stella octangula number">Stella octangula</a></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Pyramidal_number" title="Pyramidal number">pyramidal</a></th><td><ul><li><a href="/wiki/Square_pyramidal_number" title="Square pyramidal number">Square pyramidal</a></li></ul></td></tr></tbody></table></td></tr><tr><th scope="row"><a href="/wiki/Four-dimensional_space" title="Four-dimensional space">4-dimensional</a></th><td><table><tbody><tr><th scope="row">non-centered</th><td><ul><li><a href="/wiki/Pentatope_number" title="Pentatope number">Pentatope</a></li><li><a href="/wiki/Squared_triangular_number" title="Squared triangular number">Squared triangular</a></li><li><a href="/wiki/Fourth_power" title="Fourth power">Tesseractic</a></li></ul></td></tr></tbody></table></td></tr></tbody></table></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button>Combinatorial numbers</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Bell_number" title="Bell number">Bell</a></li><li><a href="/wiki/Cake_number" title="Cake number">Cake</a></li><li><a>Catalan</a></li><li><a href="/wiki/Dedekind_number" title="Dedekind number">Dedekind</a></li><li><a href="/wiki/Delannoy_number" title="Delannoy number">Delannoy</a></li><li><a href="/wiki/Euler_number" title="Euler number">Euler</a></li><li><a href="/wiki/Eulerian_number" title="Eulerian number">Eulerian</a></li><li><a href="/wiki/Fuss%E2%80%93Catalan_number" title="Fuss–Catalan number">Fuss–Catalan</a></li><li><a href="/wiki/Lah_number" title="Lah number">Lah</a></li><li><a href="/wiki/Lazy_caterer%27s_sequence" title="Lazy caterer's sequence">Lazy caterer's sequence</a></li><li><a href="/wiki/Lobb_number" title="Lobb number">Lobb</a></li><li><a href="/wiki/Motzkin_number" title="Motzkin number">Motzkin</a></li><li><a href="/wiki/Narayana_number" title="Narayana number">Narayana</a></li><li><a href="/wiki/Ordered_Bell_number" title="Ordered Bell number">Ordered Bell</a></li><li><a href="/wiki/Schr%C3%B6der_number" title="Schröder number">Schröder</a></li><li><a href="/wiki/Schr%C3%B6der%E2%80%93Hipparchus_number" title="Schröder–Hipparchus number">Schröder–Hipparchus</a></li><li><a href="/wiki/Stirling_numbers_of_the_first_kind" title="Stirling numbers of the first kind">Stirling first</a></li><li><a href="/wiki/Stirling_numbers_of_the_second_kind" title="Stirling numbers of the second kind">Stirling second</a></li><li><a href="/wiki/Telephone_number_(mathematics)" title="Telephone number (mathematics)">Telephone number</a></li><li><a href="/wiki/Wedderburn%E2%80%93Etherington_number" title="Wedderburn–Etherington number">Wedderburn–Etherington</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Prime_number" title="Prime number">Primes</a></th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Wieferich_prime#Wieferich_numbers" title="Wieferich prime">Wieferich</a></li><li><a href="/wiki/Wall%E2%80%93Sun%E2%80%93Sun_prime" title="Wall–Sun–Sun prime">Wall–Sun–Sun</a></li><li><a href="/wiki/Wolstenholme_prime" title="Wolstenholme prime">Wolstenholme prime</a></li><li><a href="/wiki/Wilson_prime#Wilson_numbers" title="Wilson prime">Wilson</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Pseudoprime" title="Pseudoprime">Pseudoprimes</a></th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Carmichael_number" title="Carmichael number">Carmichael number</a></li><li><a href="/wiki/Catalan_pseudoprime" title="Catalan pseudoprime">Catalan pseudoprime</a></li><li><a href="/wiki/Elliptic_pseudoprime" title="Elliptic pseudoprime">Elliptic pseudoprime</a></li><li><a href="/wiki/Euler_pseudoprime" title="Euler pseudoprime">Euler pseudoprime</a></li><li><a href="/wiki/Euler%E2%80%93Jacobi_pseudoprime" title="Euler–Jacobi pseudoprime">Euler–Jacobi pseudoprime</a></li><li><a href="/wiki/Fermat_pseudoprime" title="Fermat pseudoprime">Fermat pseudoprime</a></li><li><a href="/wiki/Frobenius_pseudoprime" title="Frobenius pseudoprime">Frobenius pseudoprime</a></li><li><a href="/wiki/Lucas_pseudoprime" title="Lucas pseudoprime">Lucas pseudoprime</a></li><li><a href="/wiki/Lucas%E2%80%93Carmichael_number" title="Lucas–Carmichael number">Lucas–Carmichael number</a></li><li><a href="/wiki/Somer%E2%80%93Lucas_pseudoprime" title="Somer–Lucas pseudoprime">Somer–Lucas pseudoprime</a></li><li><a href="/wiki/Strong_pseudoprime" title="Strong pseudoprime">Strong pseudoprime</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Arithmetic_function" title="Arithmetic function">Arithmetic functions</a> and <a href="/wiki/Arithmetic_dynamics" title="Arithmetic dynamics">dynamics</a></th></tr><tr><td colspan="2"><table><tbody><tr><th scope="row"><a href="/wiki/Divisor_function" title="Divisor function">Divisor functions</a></th><td><ul><li><a href="/wiki/Abundant_number" title="Abundant number">Abundant</a></li><li><a href="/wiki/Almost_perfect_number" title="Almost perfect number">Almost perfect</a></li><li><a href="/wiki/Arithmetic_number" title="Arithmetic number">Arithmetic</a></li><li><a href="/wiki/Betrothed_numbers" title="Betrothed numbers">Betrothed</a></li><li><a href="/wiki/Colossally_abundant_number" title="Colossally abundant number">Colossally abundant</a></li><li><a href="/wiki/Deficient_number" title="Deficient number">Deficient</a></li><li><a href="/wiki/Descartes_number" title="Descartes number">Descartes</a></li><li><a href="/wiki/Hemiperfect_number" title="Hemiperfect number">Hemiperfect</a></li><li><a href="/wiki/Highly_abundant_number" title="Highly abundant number">Highly abundant</a></li><li><a href="/wiki/Highly_composite_number" title="Highly composite number">Highly composite</a></li><li><a href="/wiki/Hyperperfect_number" title="Hyperperfect number">Hyperperfect</a></li><li><a href="/wiki/Multiply_perfect_number" title="Multiply perfect number">Multiply perfect</a></li><li><a href="/wiki/Perfect_number" title="Perfect number">Perfect</a></li><li><a href="/wiki/Practical_number" title="Practical number">Practical</a></li><li><a href="/wiki/Primitive_abundant_number" title="Primitive abundant number">Primitive abundant</a></li><li><a href="/wiki/Quasiperfect_number" title="Quasiperfect number">Quasiperfect</a></li><li><a href="/wiki/Refactorable_number" title="Refactorable number">Refactorable</a></li><li><a href="/wiki/Semiperfect_number" title="Semiperfect number">Semiperfect</a></li><li><a href="/wiki/Sublime_number" title="Sublime number">Sublime</a></li><li><a href="/wiki/Superabundant_number" title="Superabundant number">Superabundant</a></li><li><a href="/wiki/Superior_highly_composite_number" title="Superior highly composite number">Superior highly composite</a></li><li><a href="/wiki/Superperfect_number" title="Superperfect number">Superperfect</a></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Prime_omega_function" title="Prime omega function">Prime omega functions</a></th><td><ul><li><a href="/wiki/Almost_prime" title="Almost prime">Almost prime</a></li><li><a href="/wiki/Semiprime" title="Semiprime">Semiprime</a></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Euler%27s_totient_function" title="Euler's totient function">Euler's totient function</a></th><td><ul><li><a href="/wiki/Highly_cototient_number" title="Highly cototient number">Highly cototient</a></li><li><a href="/wiki/Highly_totient_number" title="Highly totient number">Highly totient</a></li><li><a href="/wiki/Noncototient" title="Noncototient">Noncototient</a></li><li><a href="/wiki/Nontotient" title="Nontotient">Nontotient</a></li><li><a href="/wiki/Perfect_totient_number" title="Perfect totient number">Perfect totient</a></li><li><a href="/wiki/Sparsely_totient_number" title="Sparsely totient number">Sparsely totient</a></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Aliquot_sequence" title="Aliquot sequence">Aliquot sequences</a></th><td><ul><li><a href="/wiki/Amicable_numbers" title="Amicable numbers">Amicable</a></li><li><a href="/wiki/Perfect_number" title="Perfect number">Perfect</a></li><li><a href="/wiki/Sociable_numbers" title="Sociable numbers">Sociable</a></li><li><a href="/wiki/Untouchable_number" title="Untouchable number">Untouchable</a></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Primorial" title="Primorial">Primorial</a></th><td><ul><li><a href="/wiki/Euclid_number" title="Euclid number">Euclid</a></li><li><a href="/wiki/Fortunate_number" title="Fortunate number">Fortunate</a></li></ul></td></tr></tbody></table></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button>Other <a href="/wiki/Prime_factor" title="Prime factor">prime factor</a> or <a href="/wiki/Divisor" title="Divisor">divisor</a> related numbers</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Blum_integer" title="Blum integer">Blum</a></li><li><a href="/wiki/Cyclic_number_(group_theory)" title="Cyclic number (group theory)">Cyclic</a></li><li><a href="/wiki/Erd%C5%91s%E2%80%93Nicolas_number" title="Erdős–Nicolas number">Erdős–Nicolas</a></li><li><a href="/wiki/Erd%C5%91s%E2%80%93Woods_number" title="Erdős–Woods number">Erdős–Woods</a></li><li><a href="/wiki/Friendly_number" title="Friendly number">Friendly</a></li><li><a href="/wiki/Giuga_number" title="Giuga number">Giuga</a></li><li><a href="/wiki/Harmonic_divisor_number" title="Harmonic divisor number">Harmonic divisor</a></li><li><a href="/wiki/Jordan%E2%80%93P%C3%B3lya_number" title="Jordan–Pólya number">Jordan–Pólya</a></li><li><a href="/wiki/Lucas%E2%80%93Carmichael_number" title="Lucas–Carmichael number">Lucas–Carmichael</a></li><li><a href="/wiki/Pronic_number" title="Pronic number">Pronic</a></li><li><a href="/wiki/Regular_number" title="Regular number">Regular</a></li><li><a href="/wiki/Rough_number" title="Rough number">Rough</a></li><li><a href="/wiki/Smooth_number" title="Smooth number">Smooth</a></li><li><a href="/wiki/Sphenic_number" title="Sphenic number">Sphenic</a></li><li><a href="/wiki/St%C3%B8rmer_number" title="Størmer number">Størmer</a></li><li><a href="/wiki/Super-Poulet_number" title="Super-Poulet number">Super-Poulet</a></li><li><a href="/wiki/Zeisel_number" title="Zeisel number">Zeisel</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Numeral_system" title="Numeral system">Numeral system</a>-dependent numbers</th></tr><tr><td colspan="2"><table><tbody><tr><th scope="row"><a href="/wiki/Arithmetic_function" title="Arithmetic function">Arithmetic functions</a><br>and <a href="/wiki/Arithmetic_dynamics" title="Arithmetic dynamics">dynamics</a></th><td><ul><li><a href="/wiki/Persistence_of_a_number" title="Persistence of a number">Persistence</a><ul><li><a href="/wiki/Additive_persistence" title="Additive persistence">Additive</a></li><li><a href="/wiki/Multiplicative_persistence" title="Multiplicative persistence">Multiplicative</a></li></ul></li></ul><table><tbody><tr><th scope="row"><a href="/wiki/Digit_sum" title="Digit sum">Digit sum</a></th><td><ul><li><a href="/wiki/Digit_sum" title="Digit sum">Digit sum</a></li><li><a href="/wiki/Digital_root" title="Digital root">Digital root</a></li><li><a href="/wiki/Self_number" title="Self number">Self</a></li><li><a href="/wiki/Sum-product_number" title="Sum-product number">Sum-product</a></li></ul></td></tr><tr><th scope="row">Digit product</th><td><ul><li><a href="/wiki/Multiplicative_digital_root" title="Multiplicative digital root">Multiplicative digital root</a></li><li><a href="/wiki/Sum-product_number" title="Sum-product number">Sum-product</a></li></ul></td></tr><tr><th scope="row">Coding-related</th><td><ul><li><a href="/wiki/Meertens_number" title="Meertens number">Meertens</a></li></ul></td></tr><tr><th scope="row">Other</th><td><ul><li><a href="/wiki/Dudeney_number" title="Dudeney number">Dudeney</a></li><li><a href="/wiki/Factorion" title="Factorion">Factorion</a></li><li><a href="/wiki/Kaprekar_number" title="Kaprekar number">Kaprekar</a></li><li><a href="/wiki/Kaprekar%27s_routine" title="Kaprekar's routine">Kaprekar's constant</a></li><li><a href="/wiki/Keith_number" title="Keith number">Keith</a></li><li><a href="/wiki/Lychrel_number" title="Lychrel number">Lychrel</a></li><li><a href="/wiki/Narcissistic_number" title="Narcissistic number">Narcissistic</a></li><li><a href="/wiki/Perfect_digit-to-digit_invariant" title="Perfect digit-to-digit invariant">Perfect digit-to-digit invariant</a></li><li><a href="/wiki/Perfect_digital_invariant" title="Perfect digital invariant">Perfect digital invariant</a><ul><li><a href="/wiki/Happy_number" title="Happy number">Happy</a></li></ul></li></ul></td></tr></tbody></table></td></tr><tr><th scope="row"><a href="/wiki/P-adic_numbers" title="P-adic numbers">P-adic numbers</a>-related</th><td><ul><li><a href="/wiki/Automorphic_number" title="Automorphic number">Automorphic</a><ul><li><a href="/wiki/Trimorphic_number" title="Trimorphic number">Trimorphic</a></li></ul></li></ul></td></tr><tr><th scope="row"><a href="/wiki/Numerical_digit" title="Numerical digit">Digit</a>-composition related</th><td><ul><li><a href="/wiki/Palindromic_number" title="Palindromic number">Palindromic</a></li><li><a href="/wiki/Pandigital_number" title="Pandigital number">Pandigital</a></li><li><a href="/wiki/Repdigit" title="Repdigit">Repdigit</a></li><li><a href="/wiki/Repunit" title="Repunit">Repunit</a></li><li><a href="/wiki/Self-descriptive_number" title="Self-descriptive number">Self-descriptive</a></li><li><a href="/wiki/Smarandache%E2%80%93Wellin_number" title="Smarandache–Wellin number">Smarandache–Wellin</a></li><li><a href="/wiki/Undulating_number" title="Undulating number">Undulating</a></li></ul></td></tr><tr><th scope="row">Digit-<a href="/wiki/Permutation" title="Permutation">permutation</a> related</th><td><ul><li><a href="/wiki/Cyclic_number" title="Cyclic number">Cyclic</a></li><li><a href="/wiki/Digit-reassembly_number" title="Digit-reassembly number">Digit-reassembly</a></li><li><a href="/wiki/Parasitic_number" title="Parasitic number">Parasitic</a></li><li><a href="/wiki/Primeval_number" title="Primeval number">Primeval</a></li><li><a href="/wiki/Transposable_integer" title="Transposable integer">Transposable</a></li></ul></td></tr><tr><th scope="row">Divisor-related</th><td><ul><li><a href="/wiki/Equidigital_number" title="Equidigital number">Equidigital</a></li><li><a href="/wiki/Extravagant_number" title="Extravagant number">Extravagant</a></li><li><a href="/wiki/Frugal_number" title="Frugal number">Frugal</a></li><li><a href="/wiki/Harshad_number" title="Harshad number">Harshad</a></li><li><a href="/wiki/Polydivisible_number" title="Polydivisible number">Polydivisible</a></li><li><a href="/wiki/Smith_number" title="Smith number">Smith</a></li><li><a href="/wiki/Vampire_number" title="Vampire number">Vampire</a></li></ul></td></tr><tr><th scope="row">Other</th><td><ul><li><a href="/wiki/Friedman_number" title="Friedman number">Friedman</a></li></ul></td></tr></tbody></table></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Binary_number" title="Binary number">Binary numbers</a></th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Evil_number" title="Evil number">Evil</a></li><li><a href="/wiki/Odious_number" title="Odious number">Odious</a></li><li><a href="/wiki/Pernicious_number" title="Pernicious number">Pernicious</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button>Generated via a <a href="/wiki/Sieve_theory" title="Sieve theory">sieve</a></th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Lucky_number" title="Lucky number">Lucky</a></li><li><a href="/wiki/Generation_of_primes" title="Generation of primes">Prime</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Sorting_algorithm" title="Sorting algorithm">Sorting</a> related</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Pancake_sorting" title="Pancake sorting">Pancake number</a></li><li><a href="/wiki/Sorting_number" title="Sorting number">Sorting number</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Natural_language" title="Natural language">Natural language</a> related</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Aronson%27s_sequence" title="Aronson's sequence">Aronson's sequence</a></li><li><a href="/wiki/Ban_number" title="Ban number">Ban</a></li></ul></td></tr></tbody></table><table><tbody><tr><th scope="col" colspan="2"><button type="button" aria-expanded="false" tabindex="0">show</button><a href="/wiki/Graphemics" title="Graphemics">Graphemics</a> related</th></tr><tr><td colspan="2"><ul><li><a href="/wiki/Strobogrammatic_number" title="Strobogrammatic number">Strobogrammatic</a></li></ul></td></tr></tbody></table>

*   [![](http://upload.wikimedia.org/wikipedia/en/thumb/e/e2/Symbol_portal_class.svg/16px-Symbol_portal_class.svg.png)](/wiki/File: Symbol_portal_class. svg "Portal") [Mathematics portal](/wiki/Portal: Mathematics "Portal: Mathematics")

*   [Germany](https://d-nb.info/gnd/1072323532)
*   [Israel](http://uli.nli.org.il/F/?func=find-b&local_base=NLX10&find_code=UID&request=987007561759805171)
*   [United States](https://id.loc.gov/authorities/sh2008005833)

NewPP limit report Parsed by mw2432 Cached time: 20230911090211 Cache expiry: 1814400 Reduced expiry: false Complications: [vary‐revision‐sha1, show‐toc] CPU time usage: 0.482 seconds Real time usage: 0.802 seconds Preprocessor visited node count: 3377/1000000 Post‐expand include size: 156996/2097152 bytes Template argument size: 1693/2097152 bytes Highest expansion depth: 14/100 Expensive parser function count: 7/500 Unstrip recursion depth: 1/20 Unstrip post‐expand size: 70832/5000000 bytes Lua time usage: 0.219/10.000 seconds Lua memory usage: 6824288/52428800 bytes Number of Wikibase entities loaded: 1/400

Transclusion expansion time report (%, ms, calls, template) 100.00% 407.974 1 -total 29.18% 119.060 1 Template: Reflist 21.92% 89.438 1 Template: Classes_of_natural_numbers 21.41% 87.354 1 Template: Navbox_with_collapsible_groups 18.18% 74.188 5 Template: Cite_journal 11.31% 46.128 1 Template: Short_description 8.25% 33.666 9 Template: Citation 7.18% 29.309 1 Template: Authority_control 5.71% 23.306 2 Template: Pagetype 4.22% 17.232 1 Template:Distinguish

Saved in parser cache with key enwiki:pcache:idhash: 216488-0! canonical and timestamp 20230911090210 and revision id 1170876495. Rendering was triggered because: page-view