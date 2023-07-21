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

## 常见操作复杂度分析
- 随机访问——常数 $O(1)$
- 在末尾插入或移除元素——均摊常数 $O(1)$
- 插入或移除元素到 vector 结尾的距离成线性 $O (n)$

## 迭代器类型
vector 的迭代器涵盖了指针所有的算术能力(`operator*，operator->，operator++，operator--，operator+，operator-，operator+=，operator-=`)， 同时 vector 支持随机存取，所以 vector 提供是 Random Access Iterator。

## 数据结构
```cpp
template <class T, class Alloc = alloc>
class vector
{
public:
    typedef T value_type;
    typedef value_type* iterator; // vector 的迭代器是普通指针
protected:
    iterator start;  // 表示目前使用空间的头
    iterator finish; // 表示目前使用空间的尾
    iterator end_of_storage; // 表示目前可用空间的尾
};
```
![[vector-elem.png]]

## 底层实现原理

### 动态增加大小： `M_insert_aux ` 函数

所谓动态增加大小，并不是在原空间之后接续新空间(因为无法保证原空间之后尚有可供配置的空间)，而是以原大小的两倍另外配置一块较大空间，然后将原内容拷贝过来， 然后才开始在原内容之后构造新元素，并释放原空间。因此，对 vector 的任何操作，一旦引起空间重新配置，指向原 vector 的所有迭代器就都失效了。

**vector 只能尾端增加空间，三部曲：**

- 配置一块更大空间
- 将原内容拷贝过去
- 释放原空间

### vector 的基本操作

`push_back()`：插入操作(末尾)

`pop_back()`：删除操作(末尾)

`erase()`：清除某范围 `[first, last)` 元素，或删除某个位置上的元素

`insert()`：从某个位置，插入 n 个元素，元素初值为x

`clear()`：清除所有元素

### uninitialized_fill_n 功能

```c
template< class ForwardIt, class Size, class T >
ForwardIt uninitialized_fill_n( ForwardIt first, Size count, const T& value );
```

函数功能是：从 first 起始，将 value 的值复制 count 个。

函数的参数：

- first - 要初始化的元素范围起始
- count - 要构造的元素数量
- value - 构造元素所用的值

### uninitialized_copy 功能

```c
template< class InputIt, class ForwardIt >
ForwardIt uninitialized_copy( InputIt first, InputIt last, ForwardIt d_first );
```

函数的功能：复制来自范围 `[first, last)` 的元素到始于 d_first 的未初始化内存。

函数的参数：

- first, last - 要复制的元素范围
- d_first - 目标范围的起始

#### [](https://github.com/steveLauwh/SGI-STL/tree/master/The%20Annotated%20STL%20Sources%20V3.3/container/sequence%20container/vector#uninitialized_fill-%E5%8A%9F%E8%83%BD)uninitialized_fill 功能

```c
template< class ForwardIt, class T >
void uninitialized_fill( ForwardIt first, ForwardIt last, const T& value );
```

函数的功能：复制给定的 value 到以 `[first, last)` 定义的未初始化内存区域。

函数的参数：

- first, last - 要初始化的元素的范围
- value - 构造元素所用的值

#### [](https://github.com/steveLauwh/SGI-STL/tree/master/The%20Annotated%20STL%20Sources%20V3.3/container/sequence%20container/vector#copy-%E5%8A%9F%E8%83%BD)copy 功能

```c
template< class InputIt, class OutputIt >
OutputIt copy( InputIt first, InputIt last, OutputIt d_first );
```

函数的功能：复制 `[first, last)` 所定义的范围中的元素到始于 d_first 的另一范围。

函数的参数：

- first, last - 要复制的元素范围
- d_first - 目标范围的起始

#### [](https://github.com/steveLauwh/SGI-STL/tree/master/The%20Annotated%20STL%20Sources%20V3.3/container/sequence%20container/vector#vector-insert-%E5%BD%A2%E5%BC%8F)vector insert 形式

```c
// 在 pos 前插入 value，底层实现 _M_insert_aux
iterator insert( iterator pos, const T& value );

// 在 pos 前插入 value 的 count 个副本，底层实现 _M_fill_insert
void insert( iterator pos, size_type count, const T& value );

// 在 pos 前插入来自范围 `[first, last)` 的元素，底层实现 _M_range_insert
template< class InputIt >
void insert( iterator pos, InputIt first, InputIt last);
```