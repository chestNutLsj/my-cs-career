## 9-3 SkipList, AVLTree, SplayTree, (2,3)-Tree 的接口效率比较
1. 就 search ()/get ()接口的效率而言，SplayTree 最优
2. 就 insert ()/put ()接口的效率而言，SkipList 最优，AVLTree 优于 (2,3)-Tree
3. 就 remove ()接口的效率而言，SkipList 最优

## 9-6 散列表中模余法以同间隔插入关键码
**描述**：假定散列表长度为 M，采用模余法。若从空开始将间隔为 T 的 M 个关键码插入其中。 试证明，若 g = gcd(M, T)为 M 和 T 的最大公约数，则有：

1. ==每个关键码均大约与 g 个关键码冲突==


2. ==若不采取排解冲突的措施，散列表的利用率约为 1/g==