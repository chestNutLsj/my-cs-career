
作为关联式容器的一种，map 容器存储的都是 pair 对象，也就是用 pair 类模板创建的键值对。其中，各个键值对的键和值可以是任意数据类型，包括 C++ 基本数据类型（int、double 等）、使用结构体或类自定义的类型。

> 通常情况下，map 容器中存储的各个键值对都选用 string 字符串作为键的类型。

与此同时，在使用 map 容器存储多个键值对时，该容器会自动根据各键值对的键的大小，按照既定的规则进行排序。默认情况下，map 容器选用 `std::less<T>` 排序规则（其中 T 表示键的数据类型），其会根据键的大小对所有键值对做升序排序。当然，根据实际情况的需要，我们可以手动指定 map 容器的排序规则，既可以选用 STL 标准库中提供的其它排序规则（比如 `std::greater<T>`），也可以自定义排序规则。

> 关于如何自定义 map 容器的排序规则，后续章节会做详细讲解。

另外需要注意的是，使用 map 容器存储的各个键值对，键的值既不能重复也不能被修改。换句话说，map 容器中存储的各个键值对不仅键的值独一无二，键的类型也会用 const 修饰，这意味着只要键值对被存储到 map 容器中，其键的值将不能再做任何修改。

> 前面提到，map 容器存储的都是 pair 类型的键值对元素，更确切的说，该容器存储的都是 pair<const K, T> 类型（其中 K 和 T 分别表示键和值的数据类型）的键值对元素。

map 容器定义在 \<map\> 头文件中，并位于 std 命名空间中。因此，如果想使用 map 容器，代码中应包含如下语句：

```
#include <map>
using namespace std;
```

map 容器的模板定义如下：

```
template < class Key,                                     // 指定键（key）的类型
           class T,                                       // 指定值（value）的类型
           class Compare = less<Key>,                     // 指定排序规则
           class Alloc = allocator<pair<const Key,T> >    // 指定分配器对象的类型
           > class map;
```

可以看到，map 容器模板有 4 个参数，其中后 2 个参数都设有默认值。大多数场景中，我们只需要设定前 2 个参数的值，有些场景可能会用到第 3 个参数，但最后一个参数几乎不会用到。

## 创建 C++ map 容器
------------------

map 容器的模板类中包含多种构造函数，因此创建 map 容器的方式也有多种，下面就几种常用的创建 map 容器的方法，做一一讲解。

1) 通过调用 map 容器类的默认构造函数，可以创建出一个空的 map 容器，比如：
```
std::map<std::string, int>myMap;
```
通过此方式创建出的 myMap 容器，初始状态下是空的，即没有存储任何键值对。鉴于空 map 容器可以根据需要随时添加新的键值对，因此创建空 map 容器是比较常用的。

2) 当然在创建 map 容器的同时，也可以进行初始化，比如：
```
std::map<std::string, int>myMap{ {"C 语言教程", 10},{"STL 教程", 20} };
```
由此，myMap 容器在初始状态下，就包含有 2 个键值对。

再次强调，map 容器中存储的键值对，其本质都是 pair 类模板创建的 pair 对象。因此，下面程序也可以创建出一模一样的 myMap 容器：
```
std::map<std::string, int>myMap{std:: make_pair ("C 语言教程", 10), std:: make_pair ("STL 教程", 20)};
```

3) 除此之外，在某些场景中，可以利用先前已创建好的 map 容器，再创建一个新的 map 容器。例如：
```
std::map<std::string, int>newMap (myMap);
```
由此，通过调用 map 容器的拷贝（复制）构造函数，即可成功创建一个和 myMap 完全一样的 newMap 容器。

C++ 11 标准中，还为 map 容器增添了移动构造函数。当有临时的 map 对象作为参数，传递给要初始化的 map 容器时，此时就会调用移动构造函数。举个例子：
```
#创建一个会返回临时 map 对象的函数
std::map<std::string,int> disMap () {
    std::map<std::string, int>tempMap{ {"C 语言教程", 10},{"STL 教程", 20} };
    return tempMap;
}
//调用 map 类模板的移动构造函数创建 newMap 容器
std::map<std::string, int>newMap (disMap ());
```

> 注意，无论是调用复制构造函数还是调用拷贝构造函数，都必须保证这 2 个容器的类型完全一致。

4) map 类模板还支持取已建 map 容器中指定区域内的键值对，创建并初始化新的 map 容器。例如：
```
std::map<std::string, int>myMap{ {"C 语言教程", 10},{"STL 教程", 20} };
std::map<std::string, int>newMap (++myMap.begin (), myMap.end ());
```
这里，通过调用 map 容器的双向迭代器，实现了在创建 newMap 容器的同时，将其初始化为包含一个 {"STL 教程", 20} 键值对的容器。

5) 当然，在以上几种创建 map 容器的基础上，我们都可以手动修改 map 容器的排序规则。默认情况下，map 容器调用 std::less\<T\> 规则，根据容器内各键值对的键的大小，对所有键值对做升序排序。

因此，如下 2 行创建 map 容器的方式，其实是等价的：
```
std::map<std::string, int>myMap{ {"C 语言教程", 10},{"STL 教程", 20} };
std::map<std::string, int, std::less<std::string> >myMap{ {"C 语言教程", 10},{"STL 教程", 20} };
```
以上 2 中创建方式生成的 myMap 容器，其内部键值对排列的顺序为：
```
<"C 语言教程", 10>  
<"STL 教程", 20>
```

下面程序手动修改了 myMap 容器的排序规则，令其作降序排序：
```
std::map<std::string, int, std::greater<std::string> >myMap{ {"C 语言教程", 10},{"STL 教程", 20} };
```
此时，myMap 容器内部键值对排列的顺序为：
```
<"STL 教程", 20>  
<"C 语言教程", 10>
```

## C++ map 容器的成员方法
-----------------

下表列出了 map 容器提供的常用成员方法以及各自的功能。

| 成员方法              | 功能                                                                                                                                                                           |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| begin ()          | 返回指向容器中第一个（注意，是已排好序的第一个）键值对的双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                                               |
| end ()            | 返回指向容器最后一个元素（注意，是已排好序的最后一个）所在位置后一个位置的双向迭代器，通常和 begin () 结合使用。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                    |
| rbegin ()         | 返回指向最后一个（注意，是已排好序的最后一个）元素的反向双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的反向双向迭代器。                                                                                             |
| rend ()           | 返回指向第一个（注意，是已排好序的第一个）元素所在位置前一个位置的反向双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的反向双向迭代器。                                                                                      |
| cbegin ()         | 和 begin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                       |
| cend ()           | 和 end () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                         |
| crbegin ()        | 和 rbegin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                      |
| crend ()          | 和 rend () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                        |
| find (key)        | 在 map 容器中查找键为 key 的键值对，如果成功找到，则返回指向该键值对的双向迭代器；反之，则返回和 end () 方法一样的迭代器。另外，如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                        |
| lower_bound (key) | 返回一个指向当前 map 容器中第一个大于或等于 key 的键值对的双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                                         |
| upper_bound (key) | 返回一个指向当前 map 容器中第一个大于 key 的键值对的迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                                              |
| equal_range (key) | 该方法返回一个 pair 对象（包含 2 个双向迭代器），其中 pair. first 和 lower_bound () 方法的返回值等价，pair. second 和 upper_bound () 方法的返回值等价。也就是说，该方法将返回一个范围，该范围中包含的键为 key 的键值对（map 容器键值对唯一，因此该范围最多包含一个键值对）。 |
| empty ()&nbsp;    | 若容器为空，则返回 true；否则 false。                                                                                                                                                     |
| size ()           | 返回当前 map 容器中存有键值对的个数。                                                                                                                                                        |
| max_size ()       | 返回 map 容器所能容纳键值对的最大个数，不同的操作系统，其返回值亦不相同。                                                                                                                                      |
| operator[]        | map 容器重载了 [] 运算符，只要知道 map 容器中某个键值对的键的值，就可以向获取数组中元素那样，通过键直接获取对应的值。                                                                                                            |
| at (key)          | 找到 map 容器中 key 键对应的值，如果找不到，该函数会引发 out_of_range 异常。                                                                                                                           |
| insert ()         | 向 map 容器中插入键值对。                                                                                                                                                              |
| erase ()          | 删除 map 容器指定位置、指定键（key）值或者指定区域内的键值对。后续章节还会对该方法做重点讲解。                                                                                                                          |
| swap ()           | 交换 2 个 map 容器中存储的键值对，这意味着，操作的 2 个键值对的类型必须相同。                                                                                                                                 |
| clear ()          | 清空 map 容器中所有的键值对，即使 map 容器的 size () 为 0。                                                                                                                                     |
| emplace ()        | 在当前 map 容器中的指定位置处构造新键值对。其效果和插入键值对一样，但效率更高。                                                                                                                                   |
| emplace_hint ()   | 在本质上和 emplace () 在 map 容器中构造新键值对的方式是一样的，不同之处在于，使用者必须为该方法提供一个指示键值对生成位置的迭代器，并作为该方法的第一个参数。                                                                                      |
| count (key)       | 在当前 map 容器中，查找键为 key 的键值对的个数并返回。注意，由于 map 容器中各键值对的键的值是唯一的，因此该函数的返回值最大为 1。                                                                                                    |

下面的样例演示了部分成员方法的用法：

```
#include <iostream>
#include <map>      // map
#include <string>       // string
using namespace std;

int main () {
    //创建空 map 容器，默认根据个键值对中键的值，对键值对做降序排序
    std::map<std::string, std::string, std::greater<std::string>>myMap;
    //调用 emplace () 方法，直接向 myMap 容器中指定位置构造新键值对
    myMap.emplace ("C 语言教程"," http://c.biancheng.net/c/" );
    myMap.emplace ("Python 教程", " http://c.biancheng.net/python/" );
    myMap.emplace ("STL 教程", " http://c.biancheng.net/stl/" );
    //输出当前 myMap 容器存储键值对的个数
    cout << "myMap size==" << myMap.size () << endl;
    //判断当前 myMap 容器是否为空
    if (! myMap.empty ()) {
        //借助 myMap 容器迭代器，将该容器的键值对逐个输出
        for (auto i = myMap.begin (); i != myMap.end (); ++i) {
            cout << i->first << " " << i->second << endl;
        }
    }  
    return 0;
}
```

程序执行结果为：
```
myMap size==3  
STL 教程 http://c.biancheng.net/stl/  
Python 教程 http://c.biancheng.net/python/  
C 语言教程 http://c.biancheng.net/c/
```

## map 容器的迭代器

无论是前面学习的序列式容器，还是关联式容器，要想实现遍历操作，就必须要用到该类型容器的迭代器。当然，map 容器也不例外。

C++ STL 标准库为 map 容器配备的是双向迭代器（bidirectional iterator）。这意味着，map 容器迭代器只能进行 `++p`、`p++`、`--p`、`p--`、`*p` 操作，并且迭代器之间只能使用 == 或者 != 运算符进行比较。

值得一提的是，相比序列式容器，map 容器提供了更多的成员方法（如表所示），通过调用它们，我们可以轻松获取具有指定含义的迭代器。

| 成员方法                    | 功能                                                                                                                                                                           |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| begin ()                | 返回指向容器中第一个（注意，是已排好序的第一个）键值对的双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                                               |
| end ()                  | 返回指向容器最后一个元素（注意，是已排好序的最后一个）所在位置后一个位置的双向迭代器，通常和 begin () 结合使用。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                    |
| rbegin ()               | 返回指向最后一个（注意，是已排好序的最后一个）元素的反向双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的反向双向迭代器。                                                                                             |
| rend ()&nbsp;           | 返回指向第一个（注意，是已排好序的第一个）元素所在位置前一个位置的反向双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的反向双向迭代器。                                                                                      |
| cbegin ()               | 和 begin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                       |
| cend ()&nbsp;           | 和 end () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                         |
| crbegin ()&nbsp;        | 和 rbegin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                      |
| crend ()&nbsp;          | 和 rend () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的键值对。                                                                                                                        |
| find (key)              | 在 map 容器中查找键为 key 的键值对，如果成功找到，则返回指向该键值对的双向迭代器；反之，则返回和 end () 方法一样的迭代器。另外，如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                        |
| lower_bound (key)       | 返回一个指向当前 map 容器中第一个大于或等于 key 的键值对的双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                                         |
| upper_bound (key)&nbsp; | 返回一个指向当前 map 容器中第一个大于 key 的键值对的迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。                                                                                              |
| equal_range (key)       | 该方法返回一个 pair 对象（包含 2 个双向迭代器），其中 pair. first 和 lower_bound () 方法的返回值等价，pair. second 和 upper_bound () 方法的返回值等价。也就是说，该方法将返回一个范围，该范围中包含的键为 key 的键值对（map 容器键值对唯一，因此该范围最多包含一个键值对）。 |

表中多数的成员方法，诸如 begin ()、end () 等，在学习序列式容器时已经多次使用过，它们的功能如图 2 所示。

![[map-operate.png]]

> 注意，图中 Ei 表示的是 pair 类对象，即键值对。对于 map 容器来说，每个键值对的键的值都必须保证是唯一的。

### begin/end
下面程序以 begin ()/end () 组合为例，演示了如何遍历 map 容器：

```
#include <iostream>
#include <map>      // pair
#include <string>       // string
using namespace std;

int main() {
    //创建并初始化 map 容器
    std::map<std::string, std::string>myMap{ {"STL教程","http://c.biancheng.net/stl/"},{"C语言教程","http://c.biancheng.net/c/"} };

    //调用 begin()/end() 组合，遍历 map 容器
    for (auto iter = myMap.begin(); iter != myMap.end(); ++iter) {
        cout << iter->first << " " << iter->second << endl;
    }
    return 0;
}
```

程序执行结果为：
```
C 语言教程 http://c.biancheng.net/c/  
STL 教程 http://c.biancheng.net/stl/
```

### find
除此之外，map 类模板中还提供了 find () 成员方法，它能帮我们查找指定 key 值的键值对，如果成功找到，则返回一个指向该键值对的双向迭代器；反之，其功能和 end () 方法相同。

举个例子：

```
#include <iostream>
#include <map>      // pair
#include <string>       // string
using namespace std;

int main() {
    //创建并初始化 map 容器
    std::map<std::string, std::string>myMap{ {"STL教程","http://c.biancheng.net/stl/"},
                                             {"C语言教程","http://c.biancheng.net/c/"},
                                             {"Java教程","http://c.biancheng.net/java/"} };
    //查找键为 "Java教程" 的键值对
    auto iter = myMap.find("Java教程");
    //从 iter 开始，遍历 map 容器
    for (; iter != myMap.end(); ++iter) {
        cout << iter->first << " " << iter->second << endl;
    }
    return 0;
}
```

程序执行结果为：
```
Java 教程 http://c.biancheng.net/java/  
STL 教程 http://c.biancheng.net/stl/
```

此程序中，创建并初始化的 myMap 容器，默认会根据各键值对中键的值，对各键值对做升序排序，其排序的结果为：
```
<"C 语言教程","http://c.biancheng.net/c/">  
<"Java 教程","http://c.biancheng.net/java/">  
<"STL 教程","http://c.biancheng.net/stl/">
```
在此基础上，通过调用 find () 方法，我们可以得到一个指向键为 "Java 教程" 的键值对的迭代器，由此当使用 for 循环从该迭代器出开始遍历时，就只会遍历到最后 2 个键值对。

同时，map 类模板中还提供有 lower_bound (key) 和 upper_bound (key) 成员方法，它们的功能是类似的，唯一的区别在于：

*   lower_bound (key) 返回的是指向第一个键不小于 key 的键值对的迭代器；
*   upper_bound (key) 返回的是指向第一个键大于 key 的键值对的迭代器；

下面程序演示了它们的功能：

```
#include <iostream>
#include <map>      // pair
#include <string>       // string
using namespace std;

int main() {
    //创建并初始化 map 容器
    std::map<std::string, std::string>myMap{ {"STL教程","http://c.biancheng.net/stl/"},
                                             {"C语言教程","http://c.biancheng.net/c/"},
                                             {"Java教程","http://c.biancheng.net/java/"} };
    //找到第一个键的值不小于 "Java教程" 的键值对
    auto iter = myMap.lower_bound("Java教程");
    cout << "lower：" << iter->first << " " << iter->second << endl;
   
    //找到第一个键的值大于 "Java教程" 的键值对
    iter = myMap.upper_bound("Java教程");
    cout <<"upper：" << iter->first << " " << iter->second << endl;
    return 0;
}
```

程序执行结果为：

lower：Java 教程 http://c.biancheng.net/java/  
upper：STL 教程 http://c.biancheng.net/stl/

> lower_bound (key) 和 upper_bound (key) 更多用于 multimap 容器，在 map 容器中很少用到。

equal_range (key) 成员方法可以看做是 lower_bound (key) 和 upper_bound (key) 的结合体，该方法会返回一个 pair 对象，其中的 2 个元素都是迭代器类型，其中 pair. first 实际上就是 lower_bound (key) 的返回值，而 pair. second 则等同于 upper_bound (key) 的返回值。

显然，equal_range (key) 成员方法表示的一个范围，位于此范围中的键值对，其键的值都为 key。举个例子：

```
#include <iostream>
#include <utility>  //pair
#include <map>      // map
#include <string>       // string
using namespace std;

int main() {
    //创建并初始化 map 容器
    std::map<string, string>myMap{ {"STL教程","http://c.biancheng.net/stl/"},
                                   {"C语言教程","http://c.biancheng.net/c/"},
                                   {"Java教程","http://c.biancheng.net/java/"} };
    //创建一个 pair 对象，来接收 equal_range() 的返回值
    pair <std::map<string, string>::iterator, std::map<string, string>::iterator> myPair = myMap.equal_range("C语言教程");
    //通过遍历，输出 myPair 指定范围内的键值对
    for (auto iter = myPair.first; iter != myPair.second; ++iter) {
        cout << iter->first << " " << iter->second << endl;
    }
    return 0;
}
```

程序执行结果为：

C 语言教程 http://c.biancheng.net/c/

> 和 lower_bound (key)、upper_bound (key) 一样，该方法也更常用于 multimap 容器，因为 map 容器中各键值对的键的值都是唯一的，因此通过 map 容器调用此方法，其返回的范围内最多也只有 1 个键值对。