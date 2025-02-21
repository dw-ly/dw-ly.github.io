<!--
 * @Author: SunHebin dwlyshb@163.com
 * @Date: 2025-02-21 17:21:03
 * @LastEditors: SunHebin dwlyshb@163.com
 * @LastEditTime: 2025-02-21 17:30:39
 * @FilePath: \blog\source\_posts\C-基础语法总结.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
---
title: C++基础语法总结
date: 2024-02-21
categories:
  - 语言
  - 编程语言
  - C++
tags:
  - C++
  - 编程语言
  - 入门
  - 教程
  - 后端
---

## C++ 基础语法总结

本文总结了 C++ 的基本语法特点和使用要点。

### 1. 基本语法

#### 1.1 数据类型
```cpp
// 基本数据类型
int number = 42;
double pi = 3.14159;
char letter = 'A';
bool flag = true;

// 字符串
std::string text = "Hello, C++!";
```

#### 1.2 控制流
```cpp
// if 语句
if (condition) {
    // 代码块
} else if (other_condition) {
    // 代码块
} else {
    // 代码块
}

// for 循环
for (int i = 0; i < n; i++) {
    // 循环体
}

// while 循环
while (condition) {
    // 循环体
}
```

### 2. 函数
```cpp
// 函数定义
int add(int a, int b) {
    return a + b;
}

// 函数重载
double add(double a, double b) {
    return a + b;
}
```

### 3. 类和对象
```cpp
class Person {
private:
    std::string name;
    int age;
    
public:
    // 构造函数
    Person(std::string n, int a) : name(n), age(a) {}
    
    // 成员函数
    void display() {
        std::cout << "Name: " << name << ", Age: " << age << std::endl;
    }
};
```

更多 C++ 编程内容将在后续文章中详细介绍。
