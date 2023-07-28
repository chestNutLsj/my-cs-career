
## find ()
find () 函数本质上是一个模板函数，用于在指定范围内查找和目标元素值相等的第一个元素。

如下为 find () 函数的语法格式：
```
InputIterator find (InputIterator first, InputIterator last, const T& val);
```

其中，first 和 last 为输入迭代器，\[first, last\) 用于指定该函数的查找范围；val 为要查找的目标元素。

> 正因为 first 和 last 的类型为输入迭代器，因此该函数**适用于所有的序列式容器**。

另外，该函数会返回一个输入迭代器，当 find () 函数查找成功时，其指向的是在 \[first, last\) 区域内查找到的第一个目标元素；如果查找失败，则该迭代器的指向和 last 相同。

值得一提的是，find () 函数的底层实现，其实就是用 `==` 运算符将 val 和 \[first, last\) 区域内的元素**逐个进行比对**。这也就意味着，\[first, last\) 区域内的元素必须支持 `==` 运算符。

举个例子：
```
#include <iostream>     // std::cout
#include <algorithm>    // std::find
#include <vector>       // std::vector
using namespace std;
int main() {
    //find() 函数作用于普通数组
    char stl[] ="http://c.biancheng.net/stl/";
    //调用 find() 查找第一个字符 'c'
    char * p = find(stl, stl + strlen(stl), 'c');
    //判断是否查找成功
    if (p != stl + strlen(stl)) {
        cout << p << endl;
    }
    //find() 函数作用于容器
    std::vector<int> myvector{ 10,20,30,40,50 };
    std::vector<int>::iterator it;

    it = find(myvector.begin(), myvector.end(), 30);
    if (it != myvector.end())
        cout << "查找成功：" << *it;
    else
        cout << "查找失败";
    return 0;
}
```

程序执行结果为：
```
c.biancheng. net/stl/  
查找成功：30
```

可以看到，find () 函数除了可以作用于序列式容器，还可以作用于普通数组。

对于 find () 函数的底层实现，C++ 标准库中给出了参数代码，感兴趣的读者可自行研究：
```
template<class InputIterator, class T>
InputIterator find (InputIterator first, InputIterator last, const T& val)
{
    while (first!=last) {
        if (*first==val) return first;
        ++first;
    }
    return last;
}
```