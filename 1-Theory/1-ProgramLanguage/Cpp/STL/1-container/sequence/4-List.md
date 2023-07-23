
STL list 容器，又称双向链表容器，即该容器的底层是以双向链表的形式实现的。这意味着，list 容器中的元素可以分散存储在内存空间里，而不是必须存储在一整块连续的内存空间中。

![[list-elems-store.png]]

可以看到，list 容器中各个元素的前后顺序是靠指针来维系的，每个元素都配备了 2 个指针，分别指向它的前一个元素和后一个元素。其中第一个元素的前向指针总为 null，因为它前面没有元素；同样，尾部元素的后向指针也总为 null。

基于这样的存储结构，list 容器具有一些其它容器（array、vector 和 deque）所不具备的优势，即它可以在序列已知的任何位置快速插入或删除元素（时间复杂度为 `O(1)`）。并且在 list 容器中移动元素，也比其它容器的效率高。

使用 list 容器的缺点是，它不能像 array 和 vector 那样，通过位置直接访问元素。举个例子，如果要访问 list 容器中的第 6 个元素，它不支持 `容器对象名[6]` 这种语法格式，正确的做法是从容器中第一个元素或最后一个元素开始遍历容器，直到找到该位置。

> 实际场景中，如何需要对序列进行大量添加或删除元素的操作，而直接访问元素的需求却很少，这种情况建议使用 list 容器存储序列。

list 容器以模板类 list\<T\>（T 为存储元素的类型）的形式在 `<list>` 头文件中，并位于 std 命名空间中。因此，在使用该容器之前，代码中需要包含下面两行代码：

```
#include <list>
using namespace std;
```

## list 容器的创建
----------

根据不同的使用场景，有以下 5 种创建 list 容器的方式供选择。

1) 创建一个没有任何元素的空 list 容器：
```
std::list<int> values;
```

和空 array 容器不同，空的 list 容器在创建之后仍可以添加元素，因此创建 list 容器的方式很常用。

2) 创建一个包含 n 个元素的 list 容器：
```
std::list<int> values (10);
```

通过此方式创建 values 容器，其中包含 10 个元素，每个元素的值都为相应类型的默认值（int 类型的默认值为 0）。

3) 创建一个包含 n 个元素的 list 容器，并为每个元素指定初始值。例如：
```
std::list<int> values (10, 5);
```

如此就创建了一个包含 10 个元素并且值都为 5 个 values 容器。

4) 在已有 list 容器的情况下，通过拷贝该容器可以创建新的 list 容器。例如：
```
std::list<int> value1 (10);
std::list<int> value2 (value1);
```

注意，采用此方式，必须保证新旧容器存储的元素类型一致。

5) 通过拷贝其他类型容器（或者普通数组）中指定区域内的元素，可以创建新的 list 容器。例如：
```
//拷贝普通数组，创建 list 容器
int a[] = { 1,2,3,4,5 };
std::list<int> values (a, a+5);

//拷贝其它类型的容器，创建 list 容器
std::array<int, 5> arr{ 11,12,13,14,15 };
std::list<int>values (arr.begin ()+2, arr.end ());//拷贝 arr 容器中的{13,14,15}
```


## list 容器可用的成员函数
--------------

下表罗列出了 list 模板类提供的所有成员函数以及各自的功能。

| 成员函数             | 功能                                                   |
|------------------|------------------------------------------------------|
| begin ()         | 返回指向容器中第一个元素的双向迭代器。                                  |
| end ()           | 返回指向容器中最后一个元素所在位置的下一个位置的双向迭代器。                       |
| rbegin ()        | 返回指向最后一个元素的反向双向迭代器。                                  |
| rend ()          | 返回指向第一个元素所在位置前一个位置的反向双向迭代器。                          |
| cbegin ()        | 和 begin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。      |
| cend ()          | 和 end () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。        |
| crbegin ()&nbsp; | 和 rbegin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。     |
| crend ()         | 和 rend () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。       |
| empty ()         | 判断容器中是否有元素，若无元素，则返回 true；反之，返回 false。                |
| size ()          | 返回当前容器实际包含的元素个数。                                     |
| max_size ()      | 返回容器所能包含元素个数的最大值。这通常是一个很大的值，一般是 $2^{32}-1$ ，所以我们很少会用到这个函数。 |
| front ()         | 返回第一个元素的引用。                                          |
| back ()          | 返回最后一个元素的引用。                                         |
| assign ()        | 用新元素替换容器中原有内容。                                       |
| emplace_front () | 在容器头部生成一个元素。该函数和 push_front () 的功能相同，但效率更高。          |
| push_front ()    | 在容器头部插入一个元素。                                         |
| pop_front ()     | 删除容器头部的一个元素。                                         |
| emplace_back ()  | 在容器尾部直接生成一个元素。该函数和 push_back () 的功能相同，但效率更高。         |
| push_back ()     | 在容器尾部插入一个元素。                                         |
| pop_back ()      | 删除容器尾部的一个元素。                                         |
| emplace ()       | 在容器中的指定位置插入元素。该函数和 insert () 功能相同，但效率更高。             |
| insert ()&nbsp;  | 在容器中的指定位置插入元素。                                       |
| erase ()         | 删除容器中一个或某区域内的元素。                                     |
| swap ()          | 交换两个容器中的元素，必须保证这两个容器中存储的元素类型是相同的。                    |
| resize ()        | 调整容器的大小。                                             |
| clear ()         | 删除容器存储的所有元素。                                         |
| splice ()        | 将一个 list 容器中的元素插入到另一个容器的指定位置。                        |
| remove (val)     | 删除容器中所有等于 val 的元素。                                   |
| remove_if ()     | 删除容器中满足条件的元素。                                        |
| unique ()        | 删除容器中相邻的重复元素，只保留一个。                                  |
| merge ()         | 合并两个事先已排好序的 list 容器，并且合并之后的 list 容器依然是有序的。           |
| sort ()          | 通过更改容器中元素的位置，将它们进行排序。                                |
| reverse ()       | 反转容器中元素的顺序。                                          |

除此之外，C++ 11 标准库还新增加了 begin () 和 end () 这 2 个函数，和 list 容器包含的 begin () 和 end () 成员函数不同，标准库提供的这 2 个函数的操作对象，既可以是容器，还可以是普通数组。当操作对象是容器时，它和容器包含的 begin () 和 end () 成员函数的功能完全相同；如果操作对象是普通数组，则 begin () 函数返回的是指向数组第一个元素的指针，同样 end () 返回指向数组中最后一个元素之后一个位置的指针（注意不是最后一个元素）。

list 容器还有一个`std:: swap (x , y)`非成员函数（其中 x 和 y 是存储相同类型元素的 list 容器），它和 swap () 成员函数的功能完全相同，仅使用语法上有差异。

如下代码演示了表 2 中部分成员函数的用法：
```
#include <iostream>
#include <list>
using namespace std;

int main()
{
    //创建空的 list 容器
    std::list<double> values;
    //向容器中添加元素
    values.push_back(3.1);
    values.push_back(2.2);
    values.push_back(2.9);
    cout << "values size：" << values.size() << endl;
    //对容器中的元素进行排序
    values.sort();
    //使用迭代器输出list容器中的元素
    for (std::list<double>::iterator it = values.begin(); it != values.end(); ++it) {
        std::cout << *it << " ";
    }
    return 0;
}
```

运行结果为：
```
values size：3  
2.2 2.9 3.1
```

## list 迭代器及用法

只有运用迭代器，才能访问 list 容器中存储的各个元素。list 模板类提供了如表 1 所示的这些迭代器函数。

<table><caption>表 1 list 容器迭代器函数</caption><tbody><tr><th>迭代器函数</th><th>功能</th></tr><tr><td>begin ()</td><td>返回指向容器中第一个元素的双向迭代器（正向迭代器）。</td></tr><tr><td>end ()</td><td>返回指向容器中最后一个元素所在位置的下一个位置的双向迭代器。（正向迭代器）。</td></tr><tr><td>rbegin ()&nbsp;</td><td>返回指向最后一个元素的反向双向迭代器。</td></tr><tr><td>rend ()</td><td>返回指向第一个元素所在位置前一个位置的反向双向迭代器。</td></tr><tr><td>cbegin ()</td><td>和 begin () 功能相同，只不过在其基础上，正向迭代器增加了 const 属性，即不能用于修改元素。</td></tr><tr><td>cend ()</td><td>和 end () 功能相同，只不过在其基础上，正向迭代器增加了 const 属性，即不能用于修改元素。</td></tr><tr><td>crbegin ()</td><td>和 rbegin () 功能相同，只不过在其基础上，反向迭代器增加了 const 属性，即不能用于修改元素。</td></tr><tr><td>crend ()</td><td>和 rend () 功能相同，只不过在其基础上，反向迭代器增加了 const 属性，即不能用于修改元素。</td></tr></tbody></table>

> 除此之外，[C++](http://c.biancheng.net/cplus/) 11 新添加的 begin () 和 end () 全局函数也同样适用于 list 容器。即当操作对象为 list 容器时，其功能分别和表 1 中的 begin ()、end () 成员函数相同。

表 1 中各个成员函数的功能如图 2 所示。

![](http://c.biancheng.net/uploads/allimg/191128/2-19112Q14QE40.gif)  
图 2 list 容器迭代器的功能示意图

> 注意，list 容器的底层实现结构为双向链表，图 2 这种表示仅是为了方便理解各个迭代器函数的功能。

从图 2 可以看出，这些成员函数通常是成对使用的，即 begin ()/end ()、rbegin ()/rend ()、cbegin ()/cend ()、crbegin ()/crend () 各自成对搭配使用。其中，begin ()/end () 和 cbegin/cend () 的功能是类似的，同样 rbegin ()/rend () 和 crbegin ()/crend () 的功能是类似的。

前面章节已经详细介绍了 array、vector、deque 容器的迭代器，和它们相比，list 容器迭代器最大的不同在于，其配备的迭代器类型为双向迭代器，而不再是随机访问迭代器。

这意味着，假设 p 1 和 p 2 都是双向迭代器，则它们支持使用 ++p 1、 p 1++、 p 1--、 p 1++、 *p 1、 p 1==p 2 以及 p 1!=p 2 运算符，但不支持以下操作（其中 i 为整数）：

*   p 1[i]：不能通过下标访问 list 容器中指定位置处的元素。
*   p 1-=i、 p 1+=i、 p 1+i 、p 1-i：双向迭代器 p 1 不支持使用 -=、+=、+、- 运算符。
*   p 1<p2、 p1>p 2、 p 1<=p2、 p1>=p 2：双向迭代器 p 1、p 2 不支持使用 <、 >、 <=、 >= 比较运算符。

> 有关迭代器类别和功能的具体介绍，可阅读 《[C++ STL 迭代器](http://c.biancheng.net/view/6675.html)》一节。

下面这个程序演示了如何使用表 1 中的迭代器遍历 list 容器中的各个元素。

```
#include <iostream>
#include <list>
using namespace std;

int main()
{
    //创建 list 容器
    std::list<char> values{'h','t','t','p',':','/','/','c','.','b','i','a','n','c','h','e','n','g','.','n','e','t'};
    //使用begin()/end()迭代器函数对输出list容器中的元素
    for (std::list<char>::iterator it = values.begin(); it != values.end(); ++it) {
        std::cout << *it;
    }
    cout << endl;
    //使用 rbegin()/rend()迭代器函数输出 lsit 容器中的元素
    for (std::list<char>::reverse_iterator it = values.rbegin(); it != values.rend();++it) {
        std::cout << *it;
    }
    return 0;
}
```

输出结果为：

http://c.biancheng.net  
ten. gnehcnaib. c//:ptth

> 注意，程序中比较迭代器之间的关系，用的是 != 运算符，因为它不支持 < 等运算符。另外在实际场景中，所有迭代器函数的返回值都可以传给使用 auto 关键字定义的变量，因为编译器可以自行判断出该迭代器的类型。

值得一提的是，list 容器在进行插入（insert ()）、接合（splice ()）等操作时，都不会造成原有的 list 迭代器失效，甚至进行删除操作，而只有指向被删除元素的迭代器失效，其他迭代器不受任何影响。

举个例子：

```
#include <iostream>
#include <list>
using namespace std;

int main()
{
    //创建 list 容器
    std::list<char> values{'h','t','t','p',':','/','/','c','.','b','i','a','n','c','h','e','n','g','.','n','e','t'};
    //创建 begin 和 end 迭代器
    std::list<char>::iterator begin = values.begin();
    std::list<char>::iterator end = values.end();
    //头部和尾部插入字符 '1'
    values.insert(begin, '1');
    values.insert(end, '1');
    while (begin != end)
    {
        std::cout << *begin;
        ++begin;
    }
    return 0;
}
```

运行结果为：

http://c.biancheng.net1

可以看到，在进行插入操作之后，仍使用先前创建的迭代器遍历容器，虽然程序不会出错，但由于插入位置的不同，可能会遗漏新插入的元素。