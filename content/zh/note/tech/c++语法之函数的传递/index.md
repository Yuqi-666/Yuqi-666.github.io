---
private: false
featured: false
title: c++语法之函数的传递
authors:
  - admin
tags:
  - cpp
categories:
  - tech
summary:
---
{{< toc mobile_only=true is_open=true >}}



### 1. 函数指针（Function Pointer）
函数指针可以指向函数，允许将函数作为参数传递或赋值给变量，模拟函数对象的行为。

```cpp
#include <iostream>

// 普通函数
int add(int a, int b) {
    return a + b;
}

int multiply(int a, int b) {
    return a * b;
}

// 接受函数指针作为参数的函数
int calculate(int a, int b, int (*func)(int, int)) {
    return func(a, b); // 调用函数指针指向的函数
}

int main() {
    // 函数指针赋值
    int (*operation)(int, int) = add;
    std::cout << operation(2, 3) << std::endl; // 输出: 5

    // 传递函数作为参数
    std::cout << calculate(2, 3, add) << std::endl;      // 输出: 5
    std::cout << calculate(2, 3, multiply) << std::endl; // 输出: 6

    return 0;
}
```


### 2. 函数对象（Functor）
通过重载 `operator()` 实现的类或结构体对象，可像函数一样被调用，且能存储状态（这是函数指针不具备的优势）。

```cpp
#include <iostream>

// 函数对象类
class Adder {
private:
    int base; // 可以存储状态
public:
    Adder(int b) : base(b) {}

    // 重载()运算符
    int operator()(int x) const {
        return base + x;
    }
};

class Multiplier {
private:
    int factor;
public:
    Multiplier(int f) : factor(f) {}
    int operator()(int x) const {
        return factor * x;
    }
};

// 接受函数对象作为参数
template <typename Func>
int apply(int x, Func func) {
    return func(x); // 调用函数对象
}

int main() {
    Adder add5(5);      // 创建函数对象，存储状态5
    Multiplier mul3(3); // 创建函数对象，存储状态3

    std::cout << add5(10) << std::endl;  // 输出: 15 (5+10)
    std::cout << mul3(10) << std::endl;  // 输出: 30 (3*10)

    // 传递函数对象给函数
    std::cout << apply(10, add5) << std::endl;  // 输出: 15
    std::cout << apply(10, mul3) << std::endl;  // 输出: 30

    return 0;
}
```


### 3. Lambda 表达式（C++11 及以上）
匿名函数，可捕获上下文变量，语法简洁，常用于临时需要函数对象的场景。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> nums = {1, 2, 3, 4, 5};

    // Lambda 作为函数对象，用于排序（降序）
    std::sort(nums.begin(), nums.end(), 
              [](int a, int b) { return a > b; });

    // 输出排序结果: 5 4 3 2 1
    for (int n : nums) {
        std::cout << n << " ";
    }
    std::cout << std::endl;

    // 捕获外部变量的 Lambda
    int offset = 10;
    auto add_offset = [offset](int x) { return x + offset; };
    std::cout << add_offset(5) << std::endl; // 输出: 15

    return 0;
}
```


### 4. `std::function`（C++11 及以上）
标准库提供的通用函数包装器，可存储函数指针、函数对象、Lambda 等，统一接口类型。

```cpp
#include <iostream>
#include <functional> // 需包含此头文件

int add(int a, int b) { return a + b; }

class Multiply {
public:
    int operator()(int a, int b) const { return a * b; }
};

int main() {
    // 存储普通函数
    std::function<int(int, int)> func1 = add;
    std::cout << func1(2, 3) << std::endl; // 输出: 5

    // 存储函数对象
    std::function<int(int, int)> func2 = Multiply();
    std::cout << func2(2, 3) << std::endl; // 输出: 6

    // 存储 Lambda
    std::function<int(int, int)> func3 = [](int a, int b) {
        return a - b;
    };
    std::cout << func3(5, 2) << std::endl; // 输出: 3

    return 0;
}
```


### 总结
C++ 中"函数对象"的实现方式各有特点：
- 函数指针：简单直接，但不能存储状态。
- 函数对象（Functor）：可存储状态，适合复杂逻辑。
- Lambda 表达式：简洁灵活，适合临时使用。
- `std::function`：通用包装器，统一不同类型函数的接口。

这些机制使得 C++ 能够实现类似函数式编程的特性，在 STL 算法（如 `sort`、`for_each`）中广泛应用。