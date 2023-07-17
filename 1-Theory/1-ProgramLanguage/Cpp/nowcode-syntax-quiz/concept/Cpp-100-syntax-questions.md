## 基本语法
### 0x01. 返回值

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

### 继承
1. 派生类对象不会建立基类的私有数据成员，所以不能访问基类的私有数据成员（❌）