
前面章节讲解了 map 容器和 multimap 容器的用法，类似地，[C++](http://c.biancheng.net/cplus/) [STL](http://c.biancheng.net/stl/) 标准库中还提供有 set 和 multiset 这 2 个容器，它们也属于关联式容器。不过，本节先讲解 set 容器，后续章节再讲解 multiset 容器。

和 map、multimap 容器不同，使用 set 容器存储的各个键值对，要求键 key 和值 value 必须相等。

举个例子，如下有 2 组键值对数据：

{<'a', 1>, <'b', 2>, <'c', 3>}  
{<'a', 'a'>, <'b', 'b'>, <'c', 'c'>}

显然，第一组数据中各键值对的键和值不相等，而第二组中各键值对的键和值对应相等。对于 set 容器来说，只能存储第 2 组键值对，而无法存储第一组键值对。

基于 set 容器的这种特性，当使用 set 容器存储键值对时，只需要为其提供各键值对中的 value 值（也就是 key 的值）即可。仍以存储上面第 2 组键值对为例，只需要为 set 容器提供 {'a','b','c'} ，该容器即可成功将它们存储起来。

通过前面的学习我们知道，map、multimap 容器都会自行根据键的大小对存储的键值对进行排序，set 容器也会如此，只不过 set 容器中各键值对的键 key 和值 value 是相等的，根据 key 排序，也就等价为根据 value 排序。

另外，使用 set 容器存储的各个元素的值必须各不相同。更重要的是，从语法上讲 set 容器并没有强制对存储元素的类型做 const 修饰，即 set 容器中存储的元素的值是可以修改的。但是，C++ 标准为了防止用户修改容器中元素的值，对所有可能会实现此操作的行为做了限制，使得在正常情况下，用户是无法做到修改 set 容器中元素的值的。

> 对于初学者来说，切勿尝试直接修改 set 容器中已存储元素的值，这很有可能破坏 set 容器中元素的有序性，最正确的修改 set 容器中元素值的做法是：先删除该元素，然后再添加一个修改后的元素。

值得一提的是，set 容器定义于 `<set>` 头文件，并位于 std 命名空间中。因此如果想在程序中使用 set 容器，该程序代码应先包含如下语句：

```
#include <set>
using namespace std;
```

注意，第二行代码不是必需的，如果不用，则后续程序中在使用 set 容器时，需手动注明 std 命名空间（强烈建议初学者使用）。

set 容器的类模板定义如下：

```
template < class T,                        // 键 key 和值 value 的类型
           class Compare = less<T>,        // 指定 set 容器内部的排序规则
           class Alloc = allocator<T>      // 指定分配器对象的类型
           > class set;
```

注意，由于 set 容器存储的各个键值对，其键和值完全相同，也就意味着它们的类型相同，因此 set 容器类模板的定义中，仅有第 1 个参数用于设定存储数据的类型。

> 对于 set 类模板中的 3 个参数，后 2 个参数自带默认值，且几乎所有场景中只需使用前 2 个参数，第 3 个参数不会用到。

创建 C++ set 容器的几种方法
------------------

常见的创建 set 容器的方法，大致有以下 5 种。

1) 调用默认构造函数，创建空的 set 容器。比如：

```
std::set<std::string> myset;
```

> 如果程序中已经默认指定了 std 命令空间，这里可以省略 std::。

由此就创建好了一个 set 容器，该容器采用默认的 `std::less<T>` 规则，会对存储的 string 类型元素做升序排序。注意，由于 set 容器支持随时向内部添加新的元素，因此创建空 set 容器的方法是经常使用的。

2) 除此之外，set 类模板还支持在创建 set 容器的同时，对其进行初始化。例如：

```
std::set<std::string> myset{"http://c.biancheng.net/java/",
                            "http://c.biancheng.net/stl/",
                            "http://c.biancheng.net/python/"};
```

由此即创建好了包含 3 个 string 元素的 myset 容器。由于其采用默认的 std::less<T> 规则，因此其内部存储 string 元素的顺序如下所示：

" http://c.biancheng.net/java/"  
" http://c.biancheng.net/python/"  
" http://c.biancheng.net/stl/"

3) set 类模板中还提供了拷贝（复制）构造函数，可以实现在创建新 set 容器的同时，将已有 set 容器中存储的所有元素全部复制到新 set 容器中。

例如，在第 2 种方式创建的 myset 容器的基础上，执行如下代码：

```
std::set<std::string> copyset (myset);
//等同于
//std::set<std::string> copyset = myset
```

该行代码在创建 copyset 容器的基础上，还会将 myset 容器中存储的所有元素，全部复制给 copyset 容器一份。

另外，C++ 11 标准还为 set 类模板新增了移动构造函数，其功能是实现创建新 set 容器的同时，利用临时的 set 容器为其初始化。比如：

```
set<string> retSet () {
    std::set<std::string> myset{ " http://c.biancheng.net/java/" ,
                            " http://c.biancheng.net/stl/" ,
                            " http://c.biancheng.net/python/" };
    return myset;
}
std::set<std::string> copyset (retSet ());
//或者
//std::set<std::string> copyset = retSet ();
```

注意，由于 retSet () 函数的返回值是一个临时 set 容器，因此在初始化 copyset 容器时，其内部调用的是 set 类模板中的移动构造函数，而非拷贝构造函数。

> 显然，无论是调用复制构造函数还是调用拷贝构造函数，都必须保证这 2 个容器的类型完全一致。

4) 在第 3 种方式的基础上，set 类模板还支持取已有 set 容器中的部分元素，来初始化新 set 容器。例如：

```
std::set<std::string> myset{ " http://c.biancheng.net/java/" ,
                    " http://c.biancheng.net/stl/" ,
                    " http://c.biancheng.net/python/" };
std::set<std::string> copyset (++myset.begin (), myset.end ());
```

由此初始化的 copyset 容器，其内部仅存有如下 2 个 string 字符串：

" http://c.biancheng.net/python/"  
" http://c.biancheng.net/stl/"

5) 以上几种方式创建的 set 容器，都采用了默认的`std::less<T>`规则。其实，借助 set 类模板定义中第 2 个参数，我们完全可以手动修改 set 容器中的排序规则。比如：

```
std::set<std::string,std::greater<string> > myset{
    " http://c.biancheng.net/java/" ,
    " http://c.biancheng.net/stl/" ,
    " http://c.biancheng.net/python/" };
```

通过选用 std::greater<string> 降序规则，myset 容器中元素的存储顺序为:

" http://c.biancheng.net/stl/"  
" http://c.biancheng.net/python/"  
" http://c.biancheng.net/java/"

C++ STL set 容器包含的成员方法
---------------------

表 1 列出了 set 容器提供的常用成员方法以及各自的功能。

<table><caption>表 1 C++ set 容器常用成员方法</caption><tbody><tr><th>成员方法</th><th>功能</th></tr><tr><td>begin ()</td><td>返回指向容器中第一个（注意，是已排好序的第一个）元素的双向迭代器。如果 set 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。</td></tr><tr><td>end ()</td><td>返回指向容器最后一个元素（注意，是已排好序的最后一个）所在位置后一个位置的双向迭代器，通常和 begin () 结合使用。如果 set 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。</td></tr><tr><td>rbegin ()</td><td>返回指向最后一个（注意，是已排好序的最后一个）元素的反向双向迭代器。如果 set 容器用 const 限定，则该方法返回的是 const 类型的反向双向迭代器。</td></tr><tr><td>rend ()</td><td>返回指向第一个（注意，是已排好序的第一个）元素所在位置前一个位置的反向双向迭代器。如果 set 容器用 const 限定，则该方法返回的是 const 类型的反向双向迭代器。</td></tr><tr><td>cbegin ()</td><td>和 begin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。</td></tr><tr><td>cend ()</td><td>和 end () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。</td></tr><tr><td>crbegin ()</td><td>和 rbegin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。</td></tr><tr><td>crend ()</td><td>和 rend () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。</td></tr><tr><td>find (val)</td><td>在 set 容器中查找值为 val 的元素，如果成功找到，则返回指向该元素的双向迭代器；反之，则返回和 end () 方法一样的迭代器。另外，如果 set 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。</td></tr><tr><td>lower_bound (val)</td><td>返回一个指向当前 set 容器中第一个大于或等于 val 的元素的双向迭代器。如果 set 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。</td></tr><tr><td>upper_bound (val)</td><td>返回一个指向当前 set 容器中第一个大于 val 的元素的迭代器。如果 set 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。</td></tr><tr><td>equal_range (val)</td><td>该方法返回一个 pair 对象（包含 2 个双向迭代器），其中 pair. first 和 lower_bound () 方法的返回值等价，pair. second 和 upper_bound () 方法的返回值等价。也就是说，该方法将返回一个范围，该范围中包含的值为 val 的元素（set 容器中各个元素是唯一的，因此该范围最多包含一个元素）。</td></tr><tr><td>empty ()</td><td>若容器为空，则返回 true；否则 false。</td></tr><tr><td>size ()</td><td>返回当前 set 容器中存有元素的个数。</td></tr><tr><td>max_size ()</td><td>返回 set 容器所能容纳元素的最大个数，不同的操作系统，其返回值亦不相同。</td></tr><tr><td>insert ()</td><td>向 set 容器中插入元素。</td></tr><tr><td>erase ()</td><td>删除 set 容器中存储的元素。</td></tr><tr><td>swap ()</td><td>交换 2 个 set 容器中存储的所有元素。这意味着，操作的 2 个 set 容器的类型必须相同。</td></tr><tr><td>clear ()</td><td>清空 set 容器中所有的元素，即令 set 容器的 size () 为 0。</td></tr><tr><td>emplace ()</td><td>在当前 set 容器中的指定位置直接构造新元素。其效果和 insert () 一样，但效率更高。</td></tr><tr><td>emplace_hint ()</td><td>在本质上和 emplace () 在 set 容器中构造新元素的方式是一样的，不同之处在于，使用者必须为该方法提供一个指示新元素生成位置的迭代器，并作为该方法的第一个参数。</td></tr><tr><td>count (val)</td><td>在当前 set 容器中，查找值为 val 的元素的个数，并返回。注意，由于 set 容器中各元素的值是唯一的，因此该函数的返回值最大为 1。</td></tr></tbody></table>

下面程序演示了表 1 中部分成员函数的用法：

```
#include <iostream>
#include <set>
#include <string>
using namespace std;

int main ()
{
    //创建空 set 容器
    std::set<std::string> myset;
    //空 set 容器不存储任何元素
    cout << "1、myset size = " << myset.size () << endl;
    //向 myset 容器中插入新元素
    myset.insert (" http://c.biancheng.net/java/" );
    myset.insert (" http://c.biancheng.net/stl/" );
    myset.insert (" http://c.biancheng.net/python/" );
    cout << "2、myset size = " << myset.size () << endl;
    //利用双向迭代器，遍历 myset
    for (auto iter = myset.begin (); iter != myset.end (); ++iter) {
        cout << *iter << endl;
    }
    return 0;
}
```

程序执行结果为：

1、myset size = 0  
2、myset size = 3  
http://c.biancheng.net/java/  
http://c.biancheng.net/python/  
http://c.biancheng.net/stl/

> 有关表 1 中其它成员方法的用法，后续章节会做详细讲解。