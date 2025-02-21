---
title: C++基础语法总结
date: 2024-02-21
author: 第五离央
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

# C++基础语法总结

## 基本语法

### 数据类型
```cpp
// 基本数据类型
int number = 42;
double pi = 3.14159;
char letter = 'A';
bool flag = true;

// 复合类型
int array[5] = {1, 2, 3, 4, 5};
struct Point {
    int x, y;
};
```

### 控制流
```cpp
// if 语句
if (condition) {
    // code
} else if (other_condition) {
    // code
} else {
    // code
}

// 循环
for (int i = 0; i < n; i++) {
    // code
}

while (condition) {
    // code
}

do {
    // code
} while (condition);
```

## 函数

### 函数定义
```cpp
// 基本函数
int add(int a, int b) {
    return a + b;
}

// 函数重载
void print(int x) { /*...*/ }
void print(double x) { /*...*/ }
void print(const string& s) { /*...*/ }
```

### 参数传递
```cpp
// 值传递
void func1(int x) { /*...*/ }

// 引用传递
void func2(int& x) { /*...*/ }

// const 引用
void func3(const string& s) { /*...*/ }
```

## 内存管理

### 动态内存
```cpp
// new 和 delete
int* p = new int(42);
delete p;

// 数组
int* arr = new int[10];
delete[] arr;
```

### 智能指针
```cpp
// unique_ptr
std::unique_ptr<int> up(new int(42));

// shared_ptr
std::shared_ptr<int> sp = std::make_shared<int>(42);
```

## 模板

### 函数模板
```cpp
template<typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}
```

### 类模板
```cpp
template<typename T>
class Container {
public:
    void add(const T& item);
    T get() const;
private:
    T data;
};
```

## 异常处理

### try-catch
```cpp
try {
    // 可能抛出异常的代码
    throw std::runtime_error("错误信息");
} catch (const std::exception& e) {
    // 处理异常
    std::cerr << e.what() << std::endl;
}
```

## STL基础

### 容器
```cpp
// vector
std::vector<int> vec = {1, 2, 3};

// map
std::map<string, int> map;
map["key"] = 42;

// string
std::string str = "Hello";
```

### 迭代器
```cpp
// 基本使用
for (auto it = vec.begin(); it != vec.end(); ++it) {
    std::cout << *it << std::endl;
}

// 范围for
for (const auto& item : vec) {
    std::cout << item << std::endl;
}
```

## 最佳实践

### 代码风格
- 使用有意义的变量名
- 保持一致的缩进
- 适当添加注释
- 遵循项目编码规范

### 性能优化
- 避免不必要的拷贝
- 合理使用引用
- 注意内存管理
- 考虑编译期优化

### 调试技巧
- 使用断言
- 合理打印日志
- 善用调试工具
- 编写单元测试

## 参考资料

- C++ Primer
- Effective C++
- C++ 标准库
- C++ 核心指南
