
## 0x01. 返回值

1. 通过 return 语句，函数可以返回一个或多个返回值。（❌）

>[! error] 返回值的个数
>在 C++11 及其后续标准中，函数本身仍然只能返回一个值。但是，可以使用 `std::tuple` 或 `std::pair` 等数据结构来返回多个值，从而实现在函数返回时返回多个参数的效果。

这是一个使用 `std::tuple` 返回多个参数的例子：

```cpp
#include <iostream>
#include <tuple>

std::tuple<int, double> myFunction() {
    int value1 = 42;
    double value2 = 3.14;
    return std::make_tuple(value1, value2);
}

int main() {
    auto result = myFunction();
    int a = std::get<0>(result);
    double b = std::get<1>(result);
    std::cout << "a: " << a << ", b: " << b << std::endl; // 输出：a: 42, b: 3.14
    return 0;
}
```

上述代码使用 `std::tuple` 来将多个值打包在一起并返回，然后在 `main` 函数中使用 `std::get` 来获取每个返回值的具体值。

除了 `std::tuple`，你还可以使用 `std::pair` 或自定义的结构体或类或指针或引用等数据结构来返回多个参数。这些方法都能实现在函数返回时返回多个值的目的。在选择返回多个值的方法时，需要考虑代码的可读性、维护性以及对不同类型返回值的支持。

## 继承
1. 派生类对象不会建立基类的私有数据成员，所以不能访问基类的私有数据成员（❌）

>[!error] 派生类究竟继承了基类的私有成员了吗？
>一开始我以为这个题是个因果逻辑的错误，但是转念一想可以动手实例化两个派生了和基类，分别比较对象的内存占用，这样就能确定是否真正继承了。因此可以编写如下代码，实际验证内存占用：

```cpp 
#include <iostream>

class Base {
private:
    int privateVar;
protected:
    int protectedVar;
public:
    int publicVar;
};

class Derived : public Base {
public:
    int derivedVar;
};

int main() {
    Base baseObj;
    Derived derivedObj;

    std::cout << "Size of Base: " << sizeof(baseObj) << " bytes" << std::endl;
    std::cout << "Size of Derived: " << sizeof(derivedObj) << " bytes" << std::endl;

    return 0;
}
```

运行这段代码，将会输出两个类对象的内存占用大小。如果派生类继承了基类的私有成员，那么派生类的对象的内存大小应该比基类的对象的内存大小要大，因为派生类对象包含了基类的成员。

而运行结果是：
```
Size of Base: 12 bytes
Size of Derived: 16 bytes
```

让我们分析这个结果：

1. `Size of Base: 12 bytes`: 基类 `Base` 包含一个私有成员 `privateVar` 和一个保护成员 `protectedVar`，以及一个公有成员 `publicVar`。由于大多数系统的 `int` 类型占用 4 个字节，这里的大小为 12 字节。
    
2. `Size of Derived: 16 bytes`: 派生类 `Derived` 继承了基类 `Base` 的成员，包括私有成员 `privateVar` 、保护成员 `protectedVar` 和公有成员 `publicVar`，并且还有自己的成员 ` derivedVar `。由于继承了基类的私有成员，导致派生类的对象大小增加。因此，大小为 12 字节（基类的大小） + 4 字节（派生类新增的成员）= 16 字节。
    

这证实了派生类确实继承了基类的私有成员，只是不能直接访问这些私有成员而已。当您在派生类中定义了成员函数或使用基类的公有/保护成员函数来访问基类的私有成员时，您可以间接地操作这些私有成员。

## 文件 IO
1. 使文件指针重新定位到文件读写的首地址的函数是：

`rewind()`：用于将文件内部的位置指针重新指向一个流(数据流/文件)的开头；rewind 单词的本义是：倒带
>[! note] 其它相似函数的功能
>- ftell () 函数用于得到文件位置指针当前位置相对于文件首的偏移字节数；
>- fseek()函数用于设置文件指针的位置；
>- ferror ()函数可以用于检查调用输入输出函数时出现的错误。

实际上 `fseek()` 也可以设置到文件开头，而且对错误处理的扩展性更好，但是使用比较复杂：[[60-IO-files#^9c9960]]

```cpp
#include <iostream>
#include <fstream>

int main() {
    std::fstream file("example.txt", std::ios::in); // 打开文件，以只读方式

    if (file.is_open()) {
        file.seekg(0, std::ios::beg); // 将文件指针移动到文件开头
        // 在这里进行读取操作...
        file.close(); // 关闭文件
    } else {
        std::cout << "Failed to open the file." << std::endl;
    }

    return 0;
}
```

## 内存操作

### `memset()` 函数

1. 判断以下代码的输出：
```cpp
#include <iostream>
#include <cstring> 
using namespace std;
 
class Parent {
public:
    virtual void output();
};
 
void Parent::output() {
    printf("Parent!");
}
 
class Son : public Parent {
public:
    virtual void output();
};
 
void Son::output() {
    printf("Son!");
}
 
int main() {
 
    Son s;
    memset(&s, 0, sizeof(s));
    Parent& p = s;
    p.output();
 
    return 0;
}
```
A. Parent!
B. Son!
C. 编译出错
D. 没有输出结果，程序运行出错

实际结果是 D。现在来分析一下 `memset()` 函数的使用方法：

>[! note] `memset()` 介绍
> `memset()` 是 C/C++ 中的一个库函数，用于将一段内存块设置为指定的值。它的函数原型如下：
> ```cpp
> void *memset (void *ptr, int value, size_t num);
> ```
> 
> - `ptr`: 指向要设置的内存块的指针。
> - `value`: 要设置的值，它以整数形式传递，但会被强制转换为 `unsigned char` 类型来设置到内存块中。
> - `num`: 要设置的字节数，即要设置的内存块的大小。
> 
> `memset()` 函数将 `ptr` 指向的内存块的前 `num` 个字节都设置为 `value` 的值。该函数通常用于将一块内存清零（设置为 0）或设置为其他特定的值。

以下是 `memset()` 的简单使用示例：

```cpp
#include <cstring>
#include <iostream>

int main() {
    int arr[5];
    std::memset(arr, 0, sizeof(arr)); // 将整个数组设置为0

    char str[20];
    std::memset(str, 'A', 10); // 将前10个字符设置为'A'

    // 输出数组和字符串的内容
    for (int i = 0; i < 5; ++i) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    std::cout << str << std::endl;

    return 0;
}
```

来自另一位大佬的详细介绍：

- memset 是以字节为单位，初始化内存块。当初始化一个字节单位的数组时，可以用 memset 把每个数组单元初始化成任何你想要的值，比如，
```
char data[10];
memset(data, 1, sizeof(data));    // right
memset(data, 0, sizeof(data));    // right
```
而在初始化其他基础类型时，则需要注意，比如,
```
int data[10];
memset(data, 0, sizeof(data));    // right
memset(data, -1, sizeof(data));    // right
memset(data, 1, sizeof(data));    // wrong, data[x] would be 0x0101 instead of 1
```

![[memset初始化的异常结果分析#`value` 参数设置为 1 错误的原因]]

- 当结构体类型中包含指针时，在使用 memset 初始化时需要小心。比如如下代码中，
```
struct Parameters {
          int x;
          int* p_x;
};
Parameters par;
par.p_x = new int[10];
memset(&par, 0, sizeof(par));
```
当memset初始化时，并不会初始化p_x指向的int数组单元的值，而会把已经分配过内存的p_x指针本身设置为0，造成内存泄漏。同理，对std::vector等数据类型，显而易见也是不应该使用memset来初始化的。

- 当结构体或类的本身或其基类中存在虚函数时，也需要谨慎使用 memset。这个问题就是在开头题目中发现的问题，如下代码中，
```
class BaseParameters
{
public:
    virtual void reset() {}
};

class MyParameters : public BaseParameters
{
public: 
    int data[3];
    int buf[3];
};

MyParameters my_pars;
memset(&my_pars, 0, sizeof(my_pars));
BaseParameters* pars = &my_pars;

//......

MyParameters* my = dynamic_cast<MyParameters*>(pars);
```
程序运行到 dynamic_cast 时发生异常。原因其实也很容易发现，我们的目的是为了初始化数据结构 MyParameters 里的 data 和 buf，正常来说需要初始化的内存空间是 sizeof(int) * 3 * 2 = 24字节，但是使用 memset 直接初始化 MyParameters 类型的数据结构时，sizeof(my_pars)却是28字节，因为为了实现多态机制，C++对有虚函数的对象会包含一个指向虚函数表(V-Table)的指针，当使用 memset 时，会把该虚函数表的指针也初始化为0，而 dynamic_cast 也使用 RTTI 技术，运行时会使用到 V-Table，**可此时由于与 V-Table 的链接已经被破坏，导致程序发生异常。**

## 类与成员
### 构造函数与析构函数

1. 假设 ClassY: publicX，即类 Y 是类 X 的派生类，则说明一个 Y 类的对象时和删 Y 类对象时，调用构造函数和析构函数的次序分别为（）

>[! done] 构造函数与析构函数在继承中调用顺序
>构造函数：先调用基类构造函数，再调用派生类
>析构函数：先解除派生类构造函数，在解除派生类
>
>类比：相当于建房子，构造：先打基脚，在往上建，析构：从上面往下拆

## 运算符
### sizeof
1. sizeof 与 strlen 的区别？
>[! done] sizeof 是函数吗？
>sizeof 是操作符，在编译阶段就获得结果，strlen 是函数调用，在运行阶段才获得值。

### 运算符重载
1. 调用重载后运算符的方法
>[! done] 调用重载运算符的两种方法
>1. `c = a + b; //implicitly using operator` 这样是隐式地直接使用重载后的运算符
>2. `c = a.operatpr+(b); //explicitly using its function name` 这样是运用运算符函数的调用格式

## 变量
### 标识符
1. 标识符命名规则
