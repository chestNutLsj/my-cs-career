---
banner: "![[vector.png]]"
---

## 特点
- vector 与 array 唯一区别是空间的运用的灵活性。
- array 是静态空间，一旦配置了就不能改变。
- vector 维护的是一个连续线性空间，所以不论其元素类型为何，普通指针都可以作为 vector 的迭代器而满足所有必要条件。
- 增加新元素，如果超过当时的容量，则容量会扩充至两倍。
- `<stl_vector.h>` 会调用 `<stl_bvector.h>` 的函数。

```c
vector<int>::iterator ivite;   // ivite 的类型就是 int*
vector<Shape>::iterator svite; // svite 的类型就是 Shape*
```

**vector 动态增加大小的实现原理 M_insert_aux 函数：**

所谓动态增加大小，并不是在原空间之后接续新空间(因为无法保证原空间之后尚有可供配置的空间)，而是以原大小的两倍另外配置一块较大空间，然后将原内容拷贝过来， 然后才开始在原内容之后构造新元素，并释放原空间。因此，对 vector 的任何操作，一旦引起空间重新配置，指向原 vector 的所有迭代器就都失效了。

**vector 只能尾端增加空间，三部曲：**

- 配置一块更大空间
- 将原内容拷贝过去
- 释放原空间

**vector 的基本操作：**

`push_back()`：插入操作(末尾)

`pop_back()`：删除操作(末尾)

`erase()`：清除某范围 `[first, last)` 元素，或删除某个位置上的元素

`insert()`：从某个位置，插入 n 个元素，元素初值为x

`clear()`：清除所有元素