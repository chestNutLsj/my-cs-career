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
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    //初始化一个空vector容量
    vector<char>value;
    //向value容器中的尾部依次添加 S、T、L 字符
    value.push_back('S');
    value.push_back('T');
    value.push_back('L');
    //调用 size() 成员函数容器中的元素个数
    printf("元素个数为：%d\n", value.size());
    //使用迭代器遍历容器
    for (auto i = value.begin(); i < value.end(); i++) {
        cout << *i << " ";
    }
    cout << endl;
    //向容器开头插入字符
    value.insert(value.begin(), 'C');
    cout << "首个元素为：" << value.at(0) << endl;
    return 0;
}
```

输出结果为：
```
元素个数为：3  
S T L  
首个元素为：C
```

## vector 的迭代器
vector 容器的迭代器也是随机访问迭代器，并且 vector 模板类提供的操作迭代器的成员函数也和 array 容器一样

| 成员函数       | 功能                                                                            |
|------------|-------------------------------------------------------------------------------|
| begin ()   | 返回指向容器中第一个元素的正向迭代器；如果是 const 类型容器，在该函数返回的是常量正向迭代器。                            |
| end ()     | 返回指向容器最后一个元素之后一个位置的正向迭代器；如果是 const 类型容器，在该函数返回的是常量正向迭代器。此函数通常和 begin () 搭配使用。 |
| rbegin ()  | 返回指向最后一个元素的反向迭代器；如果是 const 类型容器，在该函数返回的是常量反向迭代器。                              |
| rend ()    | 返回指向第一个元素之前一个位置的反向迭代器。如果是 const 类型容器，在该函数返回的是常量反向迭代器。此函数通常和 rbegin () 搭配使用。   |
| cbegin ()  | 和 begin () 功能类似，只不过其返回的迭代器类型为常量正向迭代器，不能用于修改元素。                                |
| cend ()    | 和 end () 功能相同，只不过其返回的迭代器类型为常量正向迭代器，不能用于修改元素。                                  |
| crbegin () | 和 rbegin () 功能相同，只不过其返回的迭代器类型为常量反向迭代器，不能用于修改元素。                               |
| crend ()   | 和 rend () 功能相同，只不过其返回的迭代器类型为常量反向迭代器，不能用于修改元素。                                 |

![[vector-visit.png]]

> 值得一提的是，以上函数在实际使用时，其返回值类型都可以使用 auto 关键字代替，编译器可以自行判断出该迭代器的类型。

### vector 容器迭代器的基本用法
-----------------

vector 容器迭代器最常用的功能就是遍历访问容器中存储的元素。

首先来看 begin () 和 end () 成员函数，它们分别用于指向「首元素」和「尾元素 +1」 的位置，下面程序演示了如何使用 begin () 和 end () 遍历 vector 容器并输出其中的元素：

```
#include <iostream>
//需要引入 vector 头文件
#include <vector>
using namespace std;
int main()
{
    vector<int>values{1,2,3,4,5};
    auto first = values.begin();
    auto end = values.end();
    while (first != end)
    {
        cout << *first << " ";
        ++first;
    }
    return 0;
}
```

输出结果为：
```
1 2 3 4 5
```

可以看到，迭代器对象是由 vector 对象的成员函数 begin () 和 end () 返回的。我们可以像使用普通指针那样上使用它们。比如代码中，在保存了元素值后，使用前缀 `++` 运算符对 first 进行自增，当 first 等于 end 时，所有的元素都被设完值，循环结束。

与此同时，还可以使用全局的 begin () 和 end () 函数来从容器中获取迭代器，比如将上面代码中第 8、9 行代码用如下代码替换：

```
auto first = std::begin(values);
auto end = std::end (values);
```

cbegin ()/cend () 成员函数和 begin ()/end () 唯一不同的是，前者返回的是 const 类型的正向迭代器，这就意味着，由 cbegin () 和 cend () 成员函数返回的迭代器，可以用来遍历容器内的元素，也可以访问元素，但是不能对所存储的元素进行修改。

举个例子：

```
#include <iostream>
//需要引入 vector 头文件
#include <vector>
using namespace std;
int main()
{
    vector<int>values{1,2,3,4,5};
    auto first = values.cbegin();
    auto end = values.cend();
    while (first != end)
    {
        //*first = 10;不能修改元素
        cout << *first << " ";
        ++first;
    }
    return 0;
}
```

程序第 12 行，由于 first 是 const 类型的迭代器，因此不能用于修改容器中元素的值。

vector 模板类中还提供了 rbegin () 和 rend () 成员函数，分别表示指向最后一个元素和第一个元素前一个位置的随机访问迭代器，又称它们为反向迭代器。

> 需要注意的是，在使用反向迭代器进行 ++ 或 -- 运算时，++ 指的是迭代器向左移动一位，-- 指的是迭代器向右移动一位，即这两个运算符的功能也 “互换” 了。

反向迭代器用于以逆序的方式遍历容器中的元素。例如：

```
#include <iostream>
//需要引入 vector 头文件
#include <vector>
using namespace std;
int main()
{
    vector<int>values{1,2,3,4,5};
    auto first = values.rbegin();
    auto end = values.rend();
    while (first != end)
    {
        cout << *first << " ";
        ++first;
    }
    return 0;
}
```

运行结果为：
```
5 4 3 2 1
```
可以看到，从最后一个元素开始循环，遍历输出了容器中的所有元素。结束迭代器指向第一个元素之前的位置，所以当 first 指向第一个元素并 +1 后，循环就结朿了。

当然，在上面程序中，我们也可以使用 [for 循环](http://c.biancheng.net/view/172.html)：

```
for (auto first = values.rbegin(); first != values.rend(); ++first) {
    cout << *first << " ";
}
```

crbegin ()/crend () 组合和 rbegin ()/crend () 组合唯一的区别在于，前者返回的迭代器为 const 类型，即不能用来修改容器中的元素，除此之外在使用上和后者完全相同。

### vector 容器迭代器的独特之处
-----------------

和 array 容器不同，vector 容器可以随着存储元素的增加，自行申请更多的存储空间。因此，在创建 vector 对象时，我们可以直接创建一个空的 vector 容器，并不会影响后续使用该容器。

但这会产生一个问题，即在初始化空的 vector 容器时，不能使用迭代器。也就是说，如下初始化 vector 容器的方法是不行的：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>values;
    int val = 1;
    for (auto first = values.begin(); first < values.end(); ++first, val++) {
        *first = val;
        //初始化的同时输出值
        cout << *first;
    }
    return 0;
}
```

运行程序可以看到，什么也没有输出。这是因为，对于空的 vector 容器来说，begin () 和 end () 成员函数返回的迭代器是相等的，即它们指向的是同一个位置。

> 对于空的 vector 容器来说，可以通过调用 push_back () 或者借助 resize () 成员函数实现初始化容器的目的。

除此之外，vector 容器在申请更多内存的同时，容器中的所有元素可能会被复制或移动到新的内存地址，这会导致之前创建的迭代器失效。

举个例子：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>values{1,2,3};
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    auto first = values.begin();
    auto end = values.end();
    //增加 values 的容量
    values.reserve(20);
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    while (first != end) {
        cout << *first;
        ++first;
    }
    return 0;
}
```

运行程序，显示如下信息并崩溃：
```
values 容器首个元素的地址：0096 DFE 8  
values 容器首个元素的地址：00965560
```
可以看到，values 容器在增加容量之后，首个元素的存储地址发生了改变，此时再使用先前创建的迭代器，显然是错误的。因此，为了保险起见，**每当 vector 容器的容量发生变化时，我们都要对之前创建的迭代器重新初始化一遍**：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>values{1,2,3};
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    auto first = values.begin();
    auto end = values.end();
    //增加 values 的容量
    values.reserve(20);
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    first = values.begin();
    end = values.end();
    while (first != end) {
        cout << *first ;
        ++first;
    }
    return 0;
}
```

运行结果为：
```
values 容器首个元素的地址：0164 DBE 8  
values 容器首个元素的地址：01645560  
123
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