
## C ++ STL 关联式容器是什么
----------------

通过学习所有的序列式容器不难发现，无论是哪种序列式容器，其存储的都是 C++ 基本数据类型（诸如 int、double、float、string 等）或使用结构体自定义类型的元素。例如，如下是一个存储 int 类型元素的 vector 容器：

std::vector\<int\> primes {2, 3, 5, 7, 11, 13, 17, 19};

关联式容器则大不一样，此类容器在存储元素值的同时，还会为各元素额外再配备一个值（又称为 “键”，其本质也是一个 C++ 基础数据类型或自定义类型的元素），它的功能是在使用关联式容器的过程中，如果已知目标元素的键的值，则直接通过该键就可以找到目标元素，而无需再通过遍历整个容器的方式。

> 弃用序列式容器，转而选用关联式容器存储元素，往往就是看中了关联式容器可以快速查找、读取或者删除所存储的元素，同时该类型容器插入元素的效率也比序列式容器高。

也就是说，使用关联式容器存储的元素，都是一个一个的 “键值对”（ <key,value> ），这是和序列式容器最大的不同。除此之外，序列式容器中存储的元素默认都是未经过排序的，而使用关联式容器存储的元素，**默认会根据各元素的键值的大小做升序排序**。

注意，关联式容器所具备的这些特性，归咎于 STL 标准库在实现该类型容器时，底层选用了 「红黑树」这种数据结构来组织和存储各个键值对。

## C++ STL 关联式容器种类
---------------

C++ STL 标准库提供了 4 种关联式容器，分别为 map、set、multimap、multiset，其各自的特点如表 1 所示。

| 关联式容器名称  | 特点                                                                                                                               |
|----------|----------------------------------------------------------------------------------------------------------------------------------|
| map      | 定义在 &lt; map&gt; 头文件中，使用该容器存储的数据，其各个元素的键必须是唯一的（即不能重复），该容器会根据各元素键的大小，默认进行升序排序（调用 std::less&lt; T&gt;）。                            |
| set      | 定义在 &lt; set&gt; 头文件中，使用该容器存储的数据，各个元素键和值完全相同，且各个元素的值不能重复（保证了各元素键的唯一性）。该容器会自动根据各个元素的键（其实也就是元素值）的大小进行升序排序（调用 std::less&lt; T&gt;）。 |
| multimap | 定义在 &lt; map&gt; 头文件中，和 map 容器唯一的不同在于，multimap 容器中存储元素的键可以重复。                                                                    |
| multiset | 定义在 &lt; set&gt; 头文件中，和 set 容器唯一的不同在于，multiset 容器中存储元素的值可以重复（一旦值重复，则意味着键也是重复的）。                                                  |

> 除此之外，C++ 11 还新增了 4 种哈希容器，即 unordered_map、unordered_multimap 以及 unordered_set、unordered_multiset。严格来说，它们也属于关联式容器，但由于哈希容器底层采用的是哈希表，而不是红黑树，因此本教程将它们分开进行讲解（有关哈希容器，将放在后续章节做详细讲解）。

为了让读者直观地认识到关联式容器的特性，这里为 map 容器为例，编写了一个样例（如下所示）。对于该程序，读者只需体会关联式容器的特性即可，无需纠结 map 容器的具体用法。

```
#include <iostream>
#include <map> //使用 map 容器，必须引入该头文件
#include <string>
using namespace std;
int main ()
{
    //创建一个空的 map 关联式容器，该容器中存储的键值对，其中键为 string 字符串，值也为 string 字符串类型
    map<string, string> mymap;
    //向 mymap 容器中添加数据
    mymap[" http://c.biancheng.net/c/" ] = "C 语言教程";
    mymap[" http://c.biancheng.net/python/" ] = "Python 教程";
    mymap[" http://c.biancheng.net/java/" ] = "Java 教程";

    //使用 map 容器的迭代器，遍历 mymap 容器，并输出其中存储的各个键值对
    for (map<string, string>:: iterator it = mymap.begin (); it != mymap.end (); ++it) {
        //输出各个元素中的键和值
        cout << it->first << " => " << it->second << '\n';
    }
    return 0;
}
```

程序输出结果为：
```
http://c.biancheng.net/c/ => C 语言教程  
http://c.biancheng.net/java/ => Java 教程  
http://c.biancheng.net/python/ => Python 教程
```

通过分析该程序的执行过程不难看出，mymap 关联式容器中的存储了以下 3 个键值对：
```
<"http://c.biancheng.net/c/", "C 语言教程">
<"http://c.biancheng.net/python/", "Python 教程">
<"http://c.biancheng.net/java/", "Java 教程">
```
但需要注意的一点是，由于 map 容器在存储元素时，会根据各个元素键的大小自动调整元素的顺序（默认按照升序排序），因此该容器最终存储的元素顺序为：
```
<"http://c.biancheng.net/c/", "C 语言教程">  
<"http://c.biancheng.net/java/", "Java 教程">  
<"http://c.biancheng.net/python/", "Python 教程">
```
