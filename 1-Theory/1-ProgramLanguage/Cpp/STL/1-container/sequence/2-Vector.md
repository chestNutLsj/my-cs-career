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

## vector 访问元素

### 访问 vector 容器中单个元素
-----------------

首先，vector 容器可以向普通数组那样访问存储的元素，甚至对指定下标处的元素进行修改，比如：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int> values{1,2,3,4,5};
    //获取容器中首个元素
    cout << values[0] << endl;
    //修改容器中下标为 0 的元素的值
    values[0] = values[1] + values[2] + values[3] + values[4];
    cout << values[0] << endl;
    return 0;
}
```

运行结果为：
```
1  
14
```

> 显然，vector 的索引从 0 开始，这和普通数组一样。通过使用索引，总是可以访问到 vector 容器中现有的元素。

值得一提的是，`容器名[n]` 这种获取元素的方式，需要确保下标 n 的值不会超过容器的容量（可以通过 capacity () 成员函数获取），否则会发生越界访问的错误。幸运的是，和 array 容器一样，vector 容器也提供了 at () 成员函数，当传给 at () 的索引会造成越界时，会抛出 `std::out_of_range` 异常。

举个例子：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int> values{1,2,3,4,5};
    //获取容器中首个元素
    cout << values.at(0) << endl;
    //修改容器中下标为 0 的元素的值
    values.at(0) = values.at(1) + values.at(2) + values.at(3) + values.at(4);
    cout << values.at(0) << endl;
    //下面这条语句会发生 out_of_range 异常
    //cout << values.at(5) << endl;
    return 0;
}
```

运行结果为：
```
1  
14
```

除此之外，vector 容器还提供了 2 个成员函数，即 front () 和 back ()，它们分别返回 vector 容器中第一个和最后一个元素的引用，通过利用这 2 个函数返回的引用，可以访问（甚至修改）容器中的首尾元素。

举个例子：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int> values{1,2,3,4,5};
    cout << "values 首元素为：" << values.front() << endl;
    cout << "values 尾元素为：" << values.back() << endl;
    //修改首元素
    values.front() = 10;
    cout <<"values 新的首元素为：" << values.front() << endl;
    //修改尾元素
    values.back() = 20;
    cout << "values 新的尾元素为：" << values.back() << endl;
    return 0;
}
```

输出结果为：
```
values 首元素为：1  
values 尾元素为：5  
values 新的首元素为：10  
values 新的尾元素为：20
```
另外，vector 容器还提供了 data () 成员函数，该函数的功能是返回指向容器中首个元素的[指针](http://c.biancheng.net/c/80/)。通过该指针也可以访问以及修改容器中的元素。比如：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int> values{1,2,3,4,5};
    //输出容器中第 3 个元素的值
    cout << *(values.data() + 2) << endl;
    //修改容器中第 2 个元素的值
    *(values.data() + 1) = 10;
    cout << *(values.data() + 1) << endl;
    return 0;
}
```

运行结果为：
```
3  
10
```

### 访问 vector 容器中多个元素
-----------------

如果想访问 vector 容器中多个元素，可以借助 size () 成员函数，该函数可以返回 vector 容器中实际存储的元素个数。例如：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int> values{1,2,3,4,5};
    //从下标 0 一直遍历到 size()-1 处
    for (int i = 0; i < values.size(); i++) {
        cout << values[i] << " ";
    }
    return 0;
}
```

运行结果为：
```
1 2 3 4 5
```

注意，这里不要使用 capacity () 成员函数，因为它返回的是 vector 容器的容量，而不是实际存储元素的个数，这两者是有差别的。

或者也可以使用基于范围的循环，此方式将会逐个遍历容器中的元素。比如：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int> values{1,2,3,4,5};
    for (auto&& value : values)
        cout << value << " ";
    return 0;
}
```

运行结果为：
```
1 2 3 4 5
```

另外还可以使用 vector 迭代器遍历 vector 容器，这里以 begin ()/end () 为例：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int> values{1,2,3,4,5};
    for (auto first = values.begin(); first < values.end(); ++first) {
        cout << *first << " ";
    }
    return 0;
}
```

运行结果为：
```
1 2 3 4 5
```

## vector capacity 与 size 的区别

很多初学者分不清楚 vector 容器的容量（capacity）和大小（size）之间的区别，甚至有人认为它们表达的是一个意思。本节将对 vector 容量和大小各自的含义做一个详细的介绍。

vector 容器的容量（用 capacity 表示），指的是在不分配更多内存的情况下，容器可以保存的最多元素个数；而 vector 容器的大小（用 size 表示），指的是它实际所包含的元素个数。

对于一个 vector 对象来说，通过该模板类提供的 capacity () 成员函数，可以获得当前容器的容量；通过 size () 成员函数，可以获得容器当前的大小。例如：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    std::vector<int>value{ 2,3,5,7,11,13,17,19,23,29,31,37,41,43,47 };
    value.reserve(20);
    cout << "value 容量是：" << value.capacity() << endl;
    cout << "value 大小是：" << value.size() << endl;
    return 0;
}
```

程序输出结果为：
```
value 容量是：20  
value 大小是：15
```

结合该程序的输出结果，下图可以更好的说明 vector 容器容量和大小之间的关系。

![[vector-size-capacity.png]]

显然，vector 容器的大小不能超出它的容量，在大小等于容量的基础上，只要增加一个元素，就必须分配更多的内存。注意，这里的 “更多” 并不是 1 个。换句话说，当 vector 容器的大小和容量相等时，如果再向其添加（或者插入）一个元素，vector 往往会申请多个存储空间，而不仅仅只申请 1 个。

>一旦 vector 容器的内存被重新分配，则和 vector 容器中元素相关的所有引用、指针以及迭代器，都可能会失效，最稳妥的方法就是重新生成。

举个例子：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>value{ 2,3,5,7,11,13,17,19,23,29,31,37,41,43,47 };
    cout << "value 容量是：" << value.capacity() << endl;
    cout << "value 大小是：" << value.size() << endl;
    printf("value首地址：%p\n", value.data());
    value.push_back(53);
    cout << "value 容量是(2)：" << value.capacity() << endl;
    cout << "value 大小是(2)：" << value.size() << endl;
    printf("value首地址： %p", value.data());
    return 0;
}
```

运行结果为：
```
value 容量是：15  
value 大小是：15  
value 首地址：01254 D 40  
value 容量是 (2)：22  
value 大小是 (2)：16  
value 首地址： 01254 E 80
```

可以看到，向 “已满” 的 vector 容器再添加一个元素，整个 value 容器的存储位置发生了改变，同时 vector 会一次性申请多个存储空间（具体多少，取决于底层算法的实现）。这样做的好处是，可以很大程度上减少 vector 申请空间的次数，当后续再添加元素时，就可以节省申请空间耗费的时间。

> 因此，对于 vector 容器而言，当增加新的元素时，有可能很快完成（即直接存在预留空间中）；也有可能会慢一些（扩容之后再放新元素）。

### 修改 vector 容器的容量和大小
------------------

另外，通过前面的学习我们知道，可以调用 reserve () 成员函数来增加容器的容量（但并不会改变存储元素的个数）；而通过调用成员函数 resize () 可以改变容器的大小，并且该函数也可能会导致 vector 容器容量的增加。比如说：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>value{ 2,3,5,7,11,13,17,19,23,29,31,37,41,43,47 };
    cout << "value 容量是：" << value.capacity() << endl;
    cout << "value 大小是：" << value.size() << endl;
    value.reserve(20);
    cout << "value 容量是(2)：" << value.capacity() << endl;
    cout << "value 大小是(2)：" << value.size() << endl;
    //将元素个数改变为 21 个，所以会增加 6 个默认初始化的元素
    value.resize(21);
    //将元素个数改变为 21 个，新增加的 6 个元素默认值为 99。
    //value.resize(21,99);
    //当需要减小容器的大小时，会移除多余的元素。
    //value.resize(20);
    cout << "value 容量是(3)：" << value.capacity() << endl;
    cout << "value 大小是(3)：" << value.size() << endl;
    return 0;
}
```

运行结果为：
```
value 容量是：15  
value 大小是：15  
value 容量是 (2)：20  
value 大小是 (2)：15  
value 容量是 (3)：30  
value 大小是 (3)：21
```

> 程序中给出了关于 resize () 成员函数的 3 种不同的用法，有兴趣的读者可自行查看不同用法的运行结果。

可以看到，仅通过 reserve () 成员函数增加 value 容器的容量，其大小并没有改变；但通过 resize () 成员函数改变 value 容器的大小，它的容量可能会发生改变。另外需要注意的是，通过 resize () 成员函数减少容器的大小（多余的元素会直接被删除），不会影响容器的容量。

### vector 容器容量和大小的数据类型
-------------------

在实际场景中，我们可能需要将容器的容量和大小保存在变量中，要知道 vector\<T\> 对象的容量和大小类型都是 vector\<T\>:: size_type 类型。因此，当定义一个变量去保存这些值时，可以如下所示：

```
vector<int>:: size_type cap = value.capacity ();
vector<int>:: size_type size = value.size ();
```

size_type 类型是定义在由 vector 类模板生成的 vecotr 类中的，它表示的真实类型和操作系统有关，在 32 位架构下普遍表示的是 unsigned int 类型，而在 64 位架构下普通表示 unsigned long 类型。

当然，我们还可以使用 auto 关键字代替 vector\<int\>:: size_type，比如：

```
auto cap = value.capacity ();
auto size = value.size ();
```

## 底层实现原理

STL 众多容器中，vector 是最常用的容器之一，其底层所采用的数据结构非常简单，就只是一段连续的线性内存空间。

通过分析 vector 容器的源代码不难发现，它就是使用 3 个迭代器（可以理解成指针）来表示的：

```
//_Alloc 表示内存分配器，此参数几乎不需要我们关心
template <class _Ty, class _Alloc = allocator<_Ty>>
class vector{
    ...
protected:
    pointer _Myfirst;
    pointer _Mylast;
    pointer _Myend;
};
```

其中，`_Myfirst` 指向的是 vector 容器对象的起始字节位置；`_Mylast` 指向当前最后一个元素的末尾字节；`_Myend` 指向整个 vector 容器所占用内存空间的末尾字节。

![[vector-base-pointer.png]]

如上图所示，通过这 3 个迭代器，就可以表示出一个已容纳 2 个元素，容量为 5 的 vector 容器。

在此基础上，将 3 个迭代器两两结合，还可以表达不同的含义，例如：
* `_Myfirst` 和 `_Mylast` 可以用来表示 vector 容器中目前已被使用的内存空间；
* `_Mylast` 和 `_Myend` 可以用来表示 vector 容器目前空闲的内存空间；
* `_Myfirst` 和 `_Myend` 可以用表示 vector 容器的容量。

> 对于空的 vector 容器，由于没有任何元素的空间分配，因此 `_Myfirst`、`_Mylast` 和 `_Myend` 均为 null。

通过灵活运用这 3 个迭代器，vector 容器可以轻松的实现诸如首尾标识、大小、容器、空容器判断等几乎所有的功能，比如：

```
template <class _Ty, class _Alloc = allocator<_Ty>>
class vector{
public：
    iterator begin() {return _Myfirst;}
    iterator end() {return _Mylast;}
    size_type size() const {return size_type(end() - begin());}
    size_type capacity() const {return size_type(_Myend - begin());}
    bool empty() const {return begin() == end();}
    reference operator[] (size_type n) {return *(begin() + n);}
    reference front() { return *begin();}
    reference back() {return *(end()-1);}
    ...
};
```

### vector 扩大容量的本质
--------------

另外需要指明的是，当 vector 的大小和容量相等（`size==capacity`）也就是满载时，如果再向其添加元素，那么 vector 就需要扩容。vector 容器扩容的过程需要经历以下 3 步：
1. 完全弃用现有的内存空间，重新申请更大的内存空间；
2. 将旧内存空间中的数据，按原有顺序移动到新的内存空间中；
3. 最后将旧的内存空间释放。

> 这也就解释了，为什么 vector 容器在进行扩容后，与其相关的指针、引用以及迭代器可能会失效的原因。

由此可见，vector 扩容是非常耗时的。为了降低再次分配内存空间时的成本，每次扩容时 vector 都会申请比用户需求量更多的内存空间（这也就是 vector 容量的由来，即 capacity>=size），以便后期使用。

> vector 容器扩容时，不同的编译器申请更多内存空间的量是不同的。以 VS 为例，它会扩容现有容器容量的 50%。

## 添加元素 `push_back()`