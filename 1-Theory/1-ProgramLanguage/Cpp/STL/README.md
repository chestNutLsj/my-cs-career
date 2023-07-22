## STL 是什么？
STL，英文全称 standard template library，中文可译为标准模板库或者泛型库，其包含有大量的模板类和模板函数，是 C++ 提供的一个基础模板的集合，用于完成诸如输入/输出、数学计算等功能。  
  
STL 最初由惠普实验室开发，于 1998 年被定为国际标准，正式成为 C++ 程序库的重要组成部分。值得一提的是，如今 STL 已完全被内置到支持 C++ 的编译器中，无需额外安装，这可能也是 STL 被广泛使用的原因之一。

>STL 就位于各个 C++ 的头文件中，即它并非以二进制代码的形式提供，而是以源代码的形式提供。

从根本上说，STL 是一些容器、算法和其他一些组件的集合，所有容器和算法都是总结了几十年来算法和[数据结构](http://c.biancheng.net/data_structure/)的研究成果，汇集了许多计算机专家学者经验的基础上实现的，因此可以说，STL 基本上达到了各种存储方法和相关算法的高度优化。  

>注意，这里提到的容器，本质上就是封装有数据结构的模板类，例如 list、vector、set、map 等，有关这些容器的具体用法，后续章节会做详细介绍。

## 学STL能干什么？

为了让读者清楚地了解 STL 是什么，使用 STL 编程有哪些优势，这里举一个使用 STL 的例子。  
  
以 C++ 定义数组的操作为例，在 C++ 中如果定义一个数组，可以采用如下方式：
```
int a[n];
```

这种定义数组的方法需要事先确定好数组的长度，即 n 必须为常量，这意味着，如果在实际应用中无法确定数组长度，则一般会将数组长度设为可能的最大值，但这极有可能导致存储空间的浪费。  
  
所以除此之外，还可以采用在堆空间中动态申请内存的方法，此时长度可以是变量：
```
int *p = new int[n];
```

这种定义方式可根据变量 n 动态申请内存，不会出现存储空间浪费的问题。但是，如果程序执行过程中出现空间不足的情况时，则需要加大存储空间，此时需要进行如下操作：

1. 新申请一个较大的内存空间，即执行`int * temp = new int[m];`
2. 将原内存空间的数据全部复制到新申请的内存空间中，即执行`memecpy(temp, p, sizeof(int)*n);`
3. 将原来的堆空间释放，即执行`delete [] p; p = temp;`

而完成相同的操作，如果采用 STL 标准库，则会简单很多，因为大多数操作细节将不需要程序员关心。下面是使用向量模板类 vector 实现以上功能的示例：  
```
vector <int> a; //定义 a 数组，当前数组长度为 0，但和普通数组不同的是，此数组 a 可以根据存储数据的数量自动变长。
//向数组 a 中添加 10 个元素
for (int i = 0; i < 10 ; i++)
   a.push_back(i)；
//还可以手动调整数组 a 的大小
a.resize(100);
a[90] = 100;
//还可以直接删除数组 a 中所有的元素，此时 a 的长度变为 0
a.clear();
//重新调整 a 的大小为 20，并存储 20 个 -1 元素。
a.resize(20, -1)
```

对比以上两种使用数组的方式不难看出，使用 STL 可以更加方便灵活地处理数据。所以，大家只需要系统地学习 STL，便可以集中精力去实现程序的功能，而无需再纠结某些细节如何用代码实现。

## STL 的版本
自 1998 年 ANSI/ISO [C++](http://c.biancheng.net/cplus/) 标准正式定案，C++ [STL](http://c.biancheng.net/stl/) 规范版本正式通过以后，由于其实开源的，各个 C++ 编译器厂商在此标准的基础上，实现了满足自己需求的 C++ STL 泛型库，主要包括 HP STL、SGI STL、STLport、PJ STL、Rouge Wave STL 等。  

### HP STL

HP STL 是 Alexandar Stepanov（STL 标准模板库之父，文章后续简称 Stepanov）在惠普 Palo Alto 实验室工作时，与 Meng Lee 合作完成的。HP STL 是开放源码的，即任何人都可以免费使用、复制、修改、发布和销售该软件以及相关文档，但前提是必须在相关文档中，加入 HP STL 版本信息和授权信息。  
  
HP STL 是 C++ STL 的第一个实现版本，其它版本的 C++ STL 一般是以 HP STL 为蓝本实现出来的。不过，现在已经很少直接使用此版本的 STL 了。

### SGI STL

Stepanov 在离开 HP 之后，就加入到了 SGI 公司，并和 Matt Austern 等人开发了 SGI STL。严格意义上来说，它是 HP STL 的一个继承版本。和 HP STL 一样，SGI STL 也是开源的，其源代码的可读性可非常好，并且任何人都可以修改和销售它。  
  
注意，和 STL 官方版本来说，SGI STL 只能算是一个“民间”版本，因此并不是所有支持 C++ 的编译器都支持使用 SGI STL 模板库，唯一能确定的是，[GCC](http://c.biancheng.net/gcc/)（Linux 下的 C++ 编译器）是支持的，所以 SGI STL 在 Linux 平台上的性能非常出色。  

### STLport

为了使 SGI STL 的基本代码都适用于 VC++ 和 C++ Builder 等多种编译器，俄国人 Boris Fomitchev 建立了一个 free 项目来开发 STLport，此版本 STL 是开放源码的。  

### PJ STL

PJ STL（全称为 P.J. Plauger STL）是由 P.J.Plauger（美国人，1965 年毕业于普林斯顿大学，物理专业学士）参照 HP STL 实现出来的，也是 HP STL 的一个继承版本，因此该头文件中不仅含有 HP STL 的相关授权信息，同时还有 P.J.Plauger 本人的版权信息。  

> 其实 PJ STL 是 P.J.Plauger 公司的产品，尽管该公司当时只有 3 个人。

PJ STL 被 Visual C++ 编译器所采用，但和 PH STL、SGI STL 不同的是，PJ STL 并不是开源。  

### Rouge Wave STL

该版本的 STL 是由 Rouge Wave 公司开发的，也是继承 HP STL 的一个版本，它也不是开源的。  
  
Rouge Wave STL 用于 Borland C++ Builder 编译器中，我们可以在 C++ Builder 的 Inculde 子目录中找到该 STL 的所有头文件。  
  
值得一提的是，尽管 Rouge Wave STL 的性能不是很好，但 C++ Builder 对 C++ 语言标准的支持还算不错，所以在一定程度上使 Rouge Wave STL 的表现得以改善。  
  
遗憾的是，由于 Rouge Wave STL 长期没有更新且不完全符合标准，因此 Rouge Wave STL 在 6.0 版本时改用了 STLport 版本（之后的版本也都采用了 STLport），不过考虑到和之前版本的兼容，6.0 版本中依旧保留了 Rouge Wave STL。  

> Rouge Wave 公司在 C++ 程序库领域应该说是鼎鼎大名，对 C++ 标准化的过程出力甚多。不过 Rouge Wave STL 版本不仅更新频率慢，费用还高，基于这两个原因，Borland 在 6.0 版本决定弃用 Rouge Wave STL 而改用 STLport。

## Cpp 泛型编程
C++泛型编程是一种编程范式，它允许编写独立于数据类型的通用代码。通过泛型编程，可以编写能够适用于不同数据类型的通用算法和数据结构，提高代码的重用性和可扩展性。

在 C++中，泛型编程的主要实现方式是使用模板（Templates）。模板是一种特殊的 C++机制，允许在编写代码时将类型参数化，使得代码可以针对不同的类型进行实例化。通过模板，可以将算法和数据结构与具体的数据类型解耦，使其在多种数据类型上都能工作，而不需要为每种类型单独编写代码。

泛型编程的主要优点包括：

1. 代码重用：可以编写一次通用代码，然后在不同的数据类型上进行实例化，避免重复编写类似的代码。

2. 性能优化：泛型代码在编译时进行类型推断，可以生成与具体数据类型最优化的代码，避免运行时的额外开销。

3. 高度灵活：泛型编程可以应用于各种数据类型，包括基本数据类型、用户定义的类、STL 容器等。

C++标准模板库（STL）是 C++中最著名的泛型编程实例，它提供了一组通用的模板类和函数，包括向量（vector）、链表（list）、队列（queue）、栈（stack）、映射（map）等容器和算法，使得 C++程序员可以轻松地使用这些通用的数据结构和算法，无需关心具体的数据类型。

## STL 的基本组成
**（6 个组件+13 个头文件）**

通常认为，STL 是由容器、算法、迭代器、函数对象、适配器、内存分配器这 6 部分构成，其中后面 4 部分是为前 2 部分服务的，它们各自的含义如表 1 所示。  

| STL 的组成 | 含义                                                                                                               |
|--------|------------------------------------------------------------------------------------------------------------------|
| 容器     | 一些封装数据结构的模板类，例如 vector 向量容器、list 列表容器等。                                                                          |
| 算法     | STL 提供了非常多（大约 100 个）的数据结构算法，它们都被设计成一个个的模板函数，这些算法在 std 命名空间中定义，其中大部分算法都包含在头文件 \<algorithm\> 中，少部分位于头文件 \<numeric\> 中。 |
| 迭代器    | 在 C++ STL 中，对容器中数据的读和写，是通过迭代器完成的，扮演着容器和算法之间的胶合剂。                                                                 |
| 函数对象   | 如果一个类将 () 运算符重载为成员函数，这个类就称为函数对象类，这个类的对象就是函数对象（又称仿函数）。                                                            |
| 适配器    | 可以使一个类的接口（模板的参数）适配成用户指定的形式，从而让原本不能在一起工作的两个类工作在一起。值得一提的是，容器、迭代器和函数都有适配器。                                          |
| 内存分配器  | 为容器类模板提供自定义的内存申请和释放功能，由于往往只有高级用户才有改变内存分配策略的需求，因此内存分配器对于一般用户来说，并不常用。                                              |
<center>表 1 STL 组成结构</center>

另外，在惠普实验室最初发行的版本中，STL 被组织成 48 个头文件；但在 C++ 标准中，它们被重新组织为 13 个头文件，如表 2 所示。  

![[stl-headers.png]]
<center>表 2 C++ STL 头文件</center>

按照 C++ 标准库的规定，所有标准头文件都不再有扩展名。以 \<vector\> 为例，此为无扩展名的形式，而 \<vector.h\> 为有扩展名的形式。  
  
但是，或许是为了向下兼容，或许是为了内部组织规划，某些 STL 版本同时存储具备扩展名和无扩展名的两份文件（例如 Visual C++ 支持的 Dinkumware 版本同时具备 \<vector.h\> 和 \<vector\>）；甚至有些 STL 版本同时拥有 3 种形式的头文件（例如 SGI 版本同时拥有 \<vector\>、\<vector.h\> 和 \<stl_vector.h\>）；但也有个别的 STL 版本只存在包含扩展名的头文件（例如 C++ Builder 的 RaugeWare 版本只有 \<vector.h\>）。 

> 建议读者养成良好的习惯，遵照 C++ 规范，使用无扩展名的头文件。
