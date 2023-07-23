
deque 是 double-ended queue 的缩写，又称双端队列容器。

前面章节中，我们已经系统学习了 vector 容器，值得一提的是，deque 容器和 vecotr 容器有很多相似之处，比如：

* deque 容器也擅长在序列尾部添加或删除元素（时间复杂度为 `O(1)`），而不擅长在序列中间添加或删除元素。
* deque 容器也可以根据需要修改自身的容量和大小。

和 vector 不同的是，deque 还擅长在序列头部添加或删除元素，所耗费的时间复杂度也为常数阶 `O(1)`。并且更重要的一点是，deque 容器中存储元素并不能保证所有元素都存储到连续的内存空间中。

> 当需要向序列两端频繁的添加或删除元素时，应首选 deque 容器。

deque 容器以模板类 deque\<T\>（T 为存储元素的类型）的形式在 \<deque\> 头文件中，并位于 std 命名空间中。因此，在使用该容器之前，代码中需要包含下面两行代码：

```
#include <deque>
using namespace std;
```

## 创建 deque 容器
----------------

创建 deque 容器，根据不同的实际场景，可选择使用如下几种方式。

1) 创建一个没有任何元素的空 deque 容器：
和空 array 容器不同，空的 deque 容器在创建之后可以做添加或删除元素的操作，因此这种简单创建 deque 容器的方式比较常见。
```
std::deque<int> d;
```

2) 创建一个具有 n 个元素的 deque 容器，其中每个元素都采用对应类型的默认值：
```
std::deque<int> d (10);
```
此行代码创建一个具有 10 个元素（默认都为 0）的 deque 容器。

3) 创建一个具有 n 个元素的 deque 容器，并为每个元素都指定初始值，例如：
```
std::deque<int> d (10, 5)
```
如此就创建了一个包含 10 个元素（值都为 5）的 deque 容器。

4) 在已有 deque 容器的情况下，可以通过拷贝该容器创建一个新的 deque 容器，例如：
```
std::deque<int> d1(5);
std::deque<int> d2(d1);
```
注意，采用此方式，必须保证新旧容器存储的元素类型一致。

5) 通过拷贝其他类型容器中指定区域内的元素（也可以是普通数组），可以创建一个新容器，例如：
```
//拷贝普通数组，创建deque容器
int a[] = { 1,2,3,4,5 };
std::deque<int>d(a, a + 5);
//适用于所有类型的容器
std::array<int, 5>arr{ 11,12,13,14,15 };
std::deque<int>d(arr.begin()+2, arr.end());//拷贝arr容器中的{13,14,15}
```

## deque 容器的成员函数
----------------

基于 deque 双端队列的特点，该容器包含一些 array、vector 容器都没有的成员函数。

下表罗列了 deque 容器提供的所有成员函数。

| 函数成员              | 函数功能                                                                |
|-------------------|---------------------------------------------------------------------|
| begin ()          | 返回指向容器中第一个元素的迭代器。                                                   |
| end ()            | 返回指向容器最后一个元素所在位置后一个位置的迭代器，通常和 begin () 结合使用。                        |
| rbegin ()         | 返回指向最后一个元素的迭代器。                                                     |
| rend ()           | 返回指向第一个元素所在位置前一个位置的迭代器。                                             |
| cbegin ()         | 和&nbsp; begin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。               |
| cend ()           | 和 end () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。                       |
| crbegin ()        | 和 rbegin () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。                    |
| crend ()          | 和 rend () 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改元素。                      |
| size ()           | 返回实际元素个数。                                                           |
| max_size ()       | 返回容器所能容纳元素个数的最大值。这通常是一个很大的值，一般是 $2^{32}-1$，我们很少会用到这个函数。                  |
| resize ()         | 改变实际元素的个数。                                                          |
| empty ()          | 判断容器中是否有元素，若无元素，则返回 true；反之，返回 false。                               |
| shrink \_to_fit () | 将内存减少到等于当前元素实际所使用的大小。                                               |
| at ()             | 使用经过边界检查的索引访问元素。                                                    |
| front ()          | 返回第一个元素的引用。                                                         |
| back ()           | 返回最后一个元素的引用。                                                        |
| assign ()         | 用新元素替换原有内容。                                                         |
| push_back ()      | 在序列的尾部添加一个元素。                                                       |
| push_front ()     | 在序列的头部添加一个元素。                                                       |
| pop_back ()       | 移除容器尾部的元素。                                                          |
| pop_front ()      | 移除容器头部的元素。                                                          |
| insert ()         | 在指定的位置插入一个或多个元素。                                                    |
| erase ()          | 移除一个元素或一段元素。                                                        |
| clear ()          | 移出所有的元素，容器大小变为 0。                                                   |
| swap ()           | 交换两个容器的所有元素。                                                        |
| emplace ()        | 在指定的位置直接生成一个元素。                                                     |
| emplace_front ()  | 在容器头部生成一个元素。和 push_front ()&nbsp; 的区别是，该函数直接在容器头部构造元素，省去了复制移动元素的过程。 |
| emplace_back ()   | 在容器尾部生成一个元素。和 push_back () 的区别是，该函数直接在容器尾部构造元素，省去了复制移动元素的过程。        |


> 和 vector 相比，额外增加了实现在容器头部添加和删除元素的成员函数，同时删除了 capacity ()、reserve () 和 data () 成员函数。

deque 容器还有一个`std:: swap (x , y)` 非成员函数（其中 x 和 y 是存储相同类型元素的 deque 容器），它和 swap () 成员函数的功能完全相同，仅使用语法上有差异。

如下代码演示了部分成员函数的用法：
```
#include <iostream>
#include <deque>
using namespace std;
int main()
{
    //初始化一个空deque容量
    deque<int>d;
    //向d容器中的尾部依次添加 1，2,3
    d.push_back(1); //{1}
    d.push_back(2); //{1,2}
    d.push_back(3); //{1,2,3}
    //向d容器的头部添加 0 
    d.push_front(0); //{0,1,2,3}

    //调用 size() 成员函数输出该容器存储的字符个数。
    printf("元素个数为：%d\n", d.size());
   
    //使用迭代器遍历容器
    for (auto i = d.begin(); i < d.end(); i++) {
        cout << *i << " ";
    }
    cout << endl;
    return 0;
}
```

运行结果为：
```
元素个数为：4  
0 1 2 3
```

## deque 的迭代器

deque 容器迭代器的类型为随机访问迭代器，deque 模板类提供了下表所示这些成员函数，通过调用这些函数，可以获得表示不同含义的随机访问迭代器。

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

![[deque-iter.png]]

> 值得一提的是，以上函数在实际使用时，其返回值类型都可以使用 auto 关键字代替，编译器可以自行判断出该迭代器的类型。

### deque 容器迭代器的基本用法
----------------

deque 容器迭代器常用来遍历容器中存储的各个元素。

begin () 和 end () 分别用于指向「首元素」和「尾元素 + 1」 的位置，下面程序演示了如何使用 begin () 和 end () 遍历 deque 容器并输出其中的元素：

```
#include <iostream>
#include <deque>
using namespace std;
int main()
{
    deque<int>d{1,2,3,4,5};
    //从容器首元素，遍历至最后一个元素
    for (auto i = d.begin(); i < d.end(); i++) {
        cout << *i << " ";
    }
    return 0;
}
```

运行结果为：
```
1 2 3 4 5
```
前面提到，STL 还提供有全局的 begin () 和 end () 函数，当操作对象为容器时，它们的功能是上面的 begin ()/end () 成员函数一样。例如，将上面程序中的第 8~10 行代码可以用如下代码替换：

```
for (auto i = begin(d); i < end(d); i++) {
    cout << *i << " ";
}
```

重新编译运行程序，会发现输出结果和上面一致。

cbegin ()/cend () 成员函数和 begin ()/end () 唯一不同的是，前者返回的是 const 类型的正向迭代器，这就意味着，由 cbegin () 和 cend () 成员函数返回的迭代器，可以用来遍历容器内的元素，也可以访问元素，但是不能对所存储的元素进行修改。

举个例子：

```
#include <iostream>
#include <deque>
using namespace std;
int main()
{
    deque<int>d{1,2,3,4,5};
    auto first = d.cbegin();
    auto end = d.cend();
    //常量迭代器不能用来修改容器中的元素值
    //*(first + 1) = 6;//尝试修改容器中元素 2 的值
    //*(end - 1) = 10;//尝试修改容器中元素 5 的值
    //常量迭代器可以用来遍历容器、访问容器中的元素
    while(first<end){
        cout << *first << " ";
        ++first;
    }
    return 0;
}
```

运行结果：
```
1 2 3 4 5
```
程序中，由于 first 和 end 都是常量迭代器，因此第 10、11 行修改容器内元素值的操作都是非法的。

deque 模板类中还提供了 rbegin () 和 rend () 成员函数，它们分别表示指向最后一个元素和第一个元素前一个位置的随机访问迭代器，又常称为反向迭代器（如图 2 所示）。

反向迭代器用于以逆序的方式遍历容器中的元素。例如：

```
#include <iostream>
#include <deque>
using namespace std;
int main()
{
    deque<int>d{1,2,3,4,5};
    for (auto i = d.rbegin(); i < d.rend(); i++) {
        cout << *i << " ";
    }
    return 0;
}
```

运行结果为：
```
5 4 3 2 1
```
crbegin ()/crend () 组合和 rbegin ()/crend () 组合唯一的区别在于，前者返回的迭代器为 const 类型迭代器，不能用来修改容器中的元素，除此之外在使用上和后者完全相同。

### deque 容器迭代器的使用注意事项
------------------

首先需要注意的一点是，迭代器的功能是遍历容器，在遍历的同时可以访问（甚至修改）容器中的元素，但迭代器不能用来初始化空的 deque 容器。

例如，如下代码中注释部分是错误的用法：

```
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>values;
    auto first = values.begin();
    //*first = 1;
    return 0;
}
```

> 对于空的 deque 容器来说，可以通过 push_back ()、push_front () 或者 resize () 成员函数实现向（空）deque 容器中添加元素。

除此之外，当向 deque 容器添加元素时，deque 容器会申请更多的内存空间，同时其包含的所有元素可能会被复制或移动到新的内存地址（原来占用的内存会释放），这会导致之前创建的迭代器失效。

举个例子：

```
#include <iostream>
#include <deque>
using namespace std;
int main()
{
    deque<int>d;
    d.push_back(1);
    auto first = d.begin();
    cout << *first << endl;
    //添加元素，会导致 first 失效
    d.push_back(1);
    cout << *first << endl;
    return 0;
}
```

程序中第 12 行代码，会导致程序运行崩溃，其原因就在于在创建 first 迭代器之后，deque 容器做了添加元素的操作，导致 first 失效。

> 在对容器做添加元素的操作之后，如果仍需要使用之前以创建好的迭代器，为了保险起见，一定要重新生成。

## deque 容器底层实现原理

事实上，STL 中每个容器的特性，和它底层的实现机制密切相关，deque 自然也不例外。一节中提到，deque 容器擅长在序列的头部和尾部添加或删除元素。本节将介绍 deque 容器的底层实现机制，探究其拥有此特点的原因。

想搞清楚 deque 容器的实现机制，需要先了解 deque 容器的存储结构以及 deque 容器迭代器的实现原理。

### deque 容器的存储结构
-------------

和 vector 容器采用连续的线性空间不同，deque 容器存储数据的空间是由一段一段等长的连续空间构成，各段空间之间并不一定是连续的，可以位于在内存的不同区域。

为了管理这些连续空间，deque 容器用数组（数组名假设为 map）存储着各个连续空间的首地址。

也就是说，map 数组中存储的都是指针，指向那些真正用来存储数据的各个连续空间（如图 1 所示）。

![[deque-address.png]]

通过建立 map 数组，deque 容器申请的这些分段的连续空间就能实现 “整体连续” 的效果。

换句话说，当 deque 容器需要在头部或尾部增加存储空间时，它会申请一段新的连续空间，同时在 map 数组的开头或结尾添加指向该空间的指针，由此该空间就串接到了 deque 容器的头部或尾部。

> 有读者可能会问，如果 map 数组满了怎么办？很简单，再申请一块更大的连续空间供 map 数组使用，将原有数据（很多指针）拷贝到新的 map 数组中，然后释放旧的空间。

deque 容器的分段存储结构，提高了在序列两端添加或删除元素的效率，但也使该容器迭代器的底层实现变得更复杂。

### deque 容器迭代器的底层实现
----------------

由于 deque 容器底层将序列中的元素分别存储到了不同段的连续空间中，因此要想实现迭代器的功能，必须先解决如下 2 个问题：

1. 迭代器在遍历 deque 容器时，必须能够确认各个连续空间在 map 数组中的位置；
2. 迭代器在遍历某个具体的连续空间时，必须能够判断自己是否已经处于空间的边缘位置。如果是，则一旦前进或者后退，就需要跳跃到上一个或者下一个连续空间中。

为了实现遍历 deque 容器的功能，deque 迭代器定义了如下的结构：

```
template<class T,...>
struct __deque_iterator{
    ...
    T* cur;
    T* first;
    T* last;
    map_pointer node;//map_pointer 等价于 T**
}
```

可以看到，迭代器内部包含 4 个指针，它们各自的作用为：
* cur：指向当前正在遍历的元素；
* first：指向当前连续空间的首地址；
* last：指向当前连续空间的末尾地址；
* node：它是一个二级指针，用于指向 map 数组中存储的指向当前连续空间的指针。

​借助这 4 个指针，deque 迭代器对随机访问迭代器支持的各种运算符进行了重载，能够对 deque 分段连续空间中存储的元素进行遍历。例如：

```
//当迭代器处于当前连续空间边缘的位置时，如果继续遍历，就需要跳跃到其它的连续空间中，该函数可用来实现此功能
void set_node(map_pointer new_node){
    node = new_node;//记录新的连续空间在 map 数组中的位置
    first = *new_node; //更新 first 指针
    //更新 last 指针，difference_type(buffer_size())表示每段连续空间的长度
    last = first + difference_type(buffer_size());
}
//重载 * 运算符
reference operator*() const{return *cur;}
pointer operator->() const{return &(operator *());}
//重载前置 ++ 运算符
self & operator++(){
    ++cur;
    //处理 cur 处于连续空间边缘的特殊情况
    if(cur == last){
        //调用该函数，将迭代器跳跃到下一个连续空间中
        set_node(node+1);
        //对 cur 重新赋值
        cur = first;
    }
    return *this;
}
//重置前置 -- 运算符
self& operator--(){
    //如果 cur 位于连续空间边缘，则先将迭代器跳跃到前一个连续空间中
    if(cur == first){
        set_node(node-1);
        cur == last;
    }
    --cur;
    return *this;
}
```

### deque 容器的底层实现
-------------

了解了 deque 容器底层存储序列的结构，以及 deque 容器迭代器的内部结构之后，接下来看看 deque 容器究竟是如何实现的。

deque 容器除了维护先前讲过的 map 数组，还需要维护 start、finish 这 2 个 deque 迭代器。以下为 deque 容器的定义：

```
//_Alloc为内存分配器
template<class _Ty,
    class _Alloc = allocator<_Ty>>
class deque{
    ...
protected:
    iterator start;
    iterator finish;
    map_pointer map;
...
}
```

其中，start 迭代器记录着 map 数组中首个连续空间的信息，finish 迭代器记录着 map 数组中最后一个连续空间的信息。另外需要注意的是，和普通 deque 迭代器不同，start 迭代器中的 cur 指针指向的是连续空间中首个元素；而 finish 迭代器中的 cur 指针指向的是连续空间最后一个元素的下一个位置。

因此，deque 容器的底层实现如图 2 所示。

![[Pasted image 20230723233533.png]]

​deque 容器的底层实现

借助 start 和 finish，以及 deque 迭代器中重载的诸多运算符，就可以实现 deque 容器提供的大部分成员函数，比如：

```
//begin() 成员函数
iterator begin() {return start;}
//end() 成员函数
iterator end() { return finish;}
//front() 成员函数
reference front(){return *start;}
//back() 成员函数
reference back(){
    iterator tmp = finish;
    --tmp;
    return *tmp;
}
//size() 成员函数
size_type size() const{return finish - start;}//deque迭代器重载了 - 运算符
//enpty() 成员函数
bool empty() const{return finish == start;}
```

### 参考资料

![](https://img-bc.icode.best/img_convert/fbae0963f339133f0ecae6788c16bf18.jpeg)

​

编辑切换为居中

添加图片注释，不超过 140 字（可选）

推荐一个零声教育 C/C++ 后台开发的免费公开课程，个人觉得老师讲得不错，分享给大家：[C/C++ 后台开发高级架构师，内容包括 Linux，Nginx，ZeroMQ，MySQL，Redis，fastdfs，MongoDB，ZK，流媒体，CDN，P 2 P，K 8 S，Docker，TCP/IP，协程，DPDK 等技术内容，立即学习]( https://icode.best/go?go=aHR0cHM6Ly9rZS5xcS5jb20vY291cnNlLzQxNzc3ND9mbG93VG9rZW49MTAxMzE4OQ== "C/C++后台开发高级架构师，内容包括 Linux，Nginx，ZeroMQ，MySQL，Redis，fastdfs，MongoDB，ZK，流媒体，CDN，P 2 P，K 8 S，Docker，TCP/IP，协程，DPDK 等技术内容，立即学习")

原文：[C++ STL deque 容器底层实现原理（深度剖析）]( https://mp.weixin.qq.com/s?__biz=MzIwNTIwMzAzNg==&mid=2654171934&idx=1&sn=3081d579df5f65110fb11f5e262504e5&chksm=8cf3a02dbb84293b700f10eea0db9fa5f74db6757da19a5c23217e687a3bf7cd34d1bd47d8d4&mpshare=1&scene=23&srcid=0909HOSnUoxJoy1bZKTODLRP&sharer_sharetime=1662706252577&sharer_shareid=e72d8588c0d41a3fcd82ae49add0d4e0#rd "C++ STL deque 容器底层实现原理（深度剖析）")