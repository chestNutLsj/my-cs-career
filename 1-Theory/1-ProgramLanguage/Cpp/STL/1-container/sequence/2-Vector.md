---
banner: "![[vector.png]]"
banner_x: 0.5
---

## vector 介绍
vector 容器是 STL 中最常用的容器之一，它和 array 容器非常类似，都可以看做是对 C++ 普通数组的 “升级版”。不同之处在于，array 实现的是静态数组（容量固定的数组），而 vector 实现的是一个动态数组，即可以进行元素的插入和删除，在此过程中，vector 会动态调整所占用的内存空间，整个过程无需人工干预。

vector 常被称为向量容器，因为该容器擅长在尾部插入或删除元素，在常量时间内就可以完成，时间复杂度为 `O(1)`；而对于在容器头部或者中部插入或删除元素，则花费时间要长一些（移动元素需要耗费时间），时间复杂度为线性阶 `O(n)`。

vector 容器以类模板 vector\<T\>（ T 表示存储元素的类型）的形式定义在 \<vector\> 头文件中，并位于 std 命名空间中。因此，在创建该容器之前，代码中需包含如下内容：

```
#include <vector>
using namespace std;
```

> 注意，std 命名空间也可以在使用 vector 容器时额外注明，两种方式都可以。

## 创建 vector 容器的几种方式
-----------------

创建 vector 容器的方式有很多，大致可分为以下几种。

1) 如下代码展示了如何创建存储 double 类型元素的一个 vector 容器：

```
std::vector<double> values;
```

注意，这是一个空的 vector 容器，因为容器中没有元素，所以没有为其分配空间。当添加第一个元素（比如使用 push_back () 函数）时，vector 会自动分配内存。

在创建好空容器的基础上，还可以像下面这样通过调用 reserve () 成员函数来增加容器的容量：

```cpp
values.reserve(20);
```

这样就设置了容器的内存分配，即至少可以容纳 20 个元素。注意，如果 vector 的容量在执行此语句之前，已经大于或等于 20 个元素，那么这条语句什么也不做；另外，调用 reserve () 不会影响已存储的元素，也不会生成任何元素，即 values 容器内此时仍然没有任何元素。

> 还需注意的是，如果调用 reserve () 来增加容器容量，之前创建好的任何迭代器（例如开始迭代器和结束迭代器）都可能会失效，这是因为，为了增加容器的容量，vector\<T\> 容器的元素可能已经被复制或移到了新的内存地址。所以后续再使用这些迭代器时，最好重新生成一下。

2) 除了创建空 vector 容器外，还可以在创建的同时指定初始值以及元素个数，比如：

```
std::vector<int> primes {2, 3, 5, 7, 11, 13, 17, 19};
```

这样就创建了一个含有 8 个素数的 vector 容器。

3) 在创建 vector 容器时，也可以指定元素个数：

```
std::vector<double> values (20);
```

如此，values 容器开始时就有 20 个元素，它们的默认初始值都为 0。

> 注意，圆括号 () 和大括号 {} 是有区别的，前者（例如 (20) ）表示元素的个数，而后者（例如 {20} ） 则表示 vector 容器中只有一个元素 20。

如果不想用 0 作为默认值，也可以指定一个其它值，例如：

```
std::vector<double> values (20, 1.0);
```

第二个参数指定了所有元素的初始值，因此这 20 个元素的值都是 1.0。

值得一提的是，圆括号 () 中的 2 个参数，既可以是常量，也可以用变量来表示，例如：

```
int num=20;
double value =1.0;
std::vector<double> values (num, value);
```

4) 通过存储元素类型相同的其它 vector 容器，也可以创建新的 vector 容器，例如：

```
std::vector<char>value 1 (5, 'c');
std::vector<char>value 2 (value 1);
```

由此，value 2 容器中也具有 5 个字符'c'。在此基础上，如果不想复制其它容器中所有的元素，可以用一对[指针](http://c.biancheng.net/c/80/)或者迭代器来指定初始值的范围，例如：

```
int array[]={1,2,3};
std::vector<int>values (array, array+2);//values 将保存{1,2}
std::vector<int>value 1{1,2,3,4,5};
std::vector<int>value 2 (std:: begin (value 1), std:: begin (value 1)+3);//value 2 保存{1,2,3}
```

由此，value 2 容器中就包含了 {1,2,3} 这 3 个元素。

## vector 容器包含的成员函数
----------------

相比 array 容器，vector 提供了更多了成员函数供我们使用，它们各自的功能如下表所示。

| 函数成员              | 函数功能                                                      |
|-------------------|-----------------------------------------------------------|
| begin ()          | 返回指向容器中第一个元素的迭代器。                                         |
| end ()            | 返回指向容器最后一个元素所在位置后一个位置的迭代器，通常和 begin () 结合使用。              |
| rbegin ()         | 返回指向最后一个元素的迭代器。                                           |
| rend ()           | 返回指向第一个元素所在位置前一个位置的迭代器。                                   |
| cbegin ()         | 和&nbsp; begin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。     |
| cend ()           | 和 end () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。             |
| crbegin ()        | 和 rbegin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。          |
| crend ()          | 和 rend () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。            |
| size ()           | 返回实际元素个数。                                                 |
| max_size ()       | 返回元素个数的最大值。这通常是一个很大的值，一般是 232-1，所以我们很少会用到这个函数。            |
| resize ()         | 改变实际元素的个数。                                                |
| capacity ()       | 返回当前容量。                                                   |
| empty ()          | 判断容器中是否有元素，若无元素，则返回 true；反之，返回 false。                     |
| reserve ()        | 增加容器的容量。                                                  |
| shrink \_to\_fit () | 将内存减少到等于当前元素实际所使用的大小。                                     |
| operator[ ]       | 重载了&nbsp;[ ] 运算符，可以向访问数组中元素那样，通过下标即可访问甚至修改 vector 容器中的元素。 |
| at ()             | 使用经过边界检查的索引访问元素。                                          |
| front ()          | 返回第一个元素的引用。                                               |
| back ()           | 返回最后一个元素的引用。                                              |
| data ()           | 返回指向容器中第一个元素的指针。                                          |
| assign ()         | 用新元素替换原有内容。                                               |
| push_back ()      | 在序列的尾部添加一个元素。                                             |
| pop_back ()       | 移出序列尾部的元素。                                                |
| insert ()         | 在指定的位置插入一个或多个元素。                                          |
| erase ()          | 移出一个元素或一段元素。                                              |
| clear ()          | 移出所有的元素，容器大小变为 0。                                         |
| swap ()           | 交换两个容器的所有元素。                                              |
| emplace ()        | 在指定的位置直接生成一个元素。                                           |
| emplace_back ()   | 在序列尾部生成一个元素。                                              |

vector 容器还有一个 std:: swap (x , y) 非成员函数（其中 x 和 y 是存储相同类型元素的  vector 容器），它和 swap () 成员函数的功能完全相同，仅使用语法上有差异。

如下代码演示了表 1 中部分成员函数的用法：

```
int array[]={1,2,3};
std::vector<int>values (array, array+2);//values 将保存{1,2}
std::vector<int>value 1{1,2,3,4,5};
std::vector<int>value 2 (std:: begin (value 1), std:: begin (value 1)+3);//value 2 保存{1,2,3}
```

输出结果为：
```
元素个数为：3  
S T L  
首个元素为：C
```



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

### uninitialized_fill 功能

```c
template< class ForwardIt, class T >
void uninitialized_fill( ForwardIt first, ForwardIt last, const T& value );
```

函数的功能：复制给定的 value 到以 `[first, last)` 定义的未初始化内存区域。

函数的参数：

- first, last - 要初始化的元素的范围
- value - 构造元素所用的值

### copy 功能

```c
template< class InputIt, class OutputIt >
OutputIt copy( InputIt first, InputIt last, OutputIt d_first );
```

函数的功能：复制 `[first, last)` 所定义的范围中的元素到始于 d_first 的另一范围。

函数的参数：

- first, last - 要复制的元素范围
- d_first - 目标范围的起始

### vector insert 形式

```c
// 在 pos 前插入 value，底层实现 _M_insert_aux
iterator insert( iterator pos, const T& value );

// 在 pos 前插入 value 的 count 个副本，底层实现 _M_fill_insert
void insert( iterator pos, size_type count, const T& value );

// 在 pos 前插入来自范围 `[first, last)` 的元素，底层实现 _M_range_insert
template< class InputIt >
void insert( iterator pos, InputIt first, InputIt last);
```