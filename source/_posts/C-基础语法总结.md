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

// pair和tuple
std::pair<int, string> p1 = {1, "first"};
std::tuple<int, string, double> t1 = {1, "first", 3.14};
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

// likely/unlikely优化分支预测
if (likely(x > 0)) {  // 提示编译器这个分支更可能发生
    // 常见情况
} else {
    // 罕见情况
}

// for 循环
for (int i = 0; i < n; i++) {
    // 循环体
}

// while 循环
while (condition) {
    // 循环体
}

// do-while(0) 常用于宏定义
do {
    // 代码块
    if (error) break;
    // 更多代码
} while(0);
```

### 2. 内存管理

#### 2.1 new 和 malloc
```cpp
// new/delete
int* p1 = new int;        // 单个对象
int* p2 = new int[10];    // 数组
delete p1;                // 释放单个对象
delete[] p2;              // 释放数组

// malloc/free
int* p3 = (int*)malloc(sizeof(int));
free(p3);

// new的优势：
// 1. 会调用构造函数
// 2. 可以被重载
// 3. 类型安全
// 4. 可以被异常处理捕获
```

#### 2.2 内存函数
```cpp
// memset：内存设置
char str[10];
memset(str, 0, sizeof(str));  // 清零操作

// sprintf和snprintf：格式化字符串
char buffer[50];
sprintf(buffer, "Value is %d", 123);      // 不安全
snprintf(buffer, 50, "Value is %d", 123); // 安全，限制写入大小
```

### 3. 结构体和类

#### 3.1 结构体
```cpp
// 结构体定义
typedef struct Person {
    int id;
    string name;
    
    // C++结构体可以包含函数
    void display() {
        cout << "ID: " << id << ", Name: " << name << endl;
    }
} Per;

// 结构体嵌套
struct Outer {
    struct Inner {
        int x;
    } inner;
    int y;
};
```

#### 3.2 类成员访问
```cpp
class Student {
public:
    string name;
};

// 对象访问
Student s;
s.name = "Tom";          // 使用点运算符

// 指针访问
Student* ps = new Student;
ps->name = "Jerry";      // 使用箭头运算符
(*ps).name = "Jerry";    // 等价形式
```

### 4. 关键字和修饰符

#### 4.1 static关键字
```cpp
// 1. 静态局部变量
void func() {
    static int count = 0;  // 只初始化一次
    count++;
}

// 2. 静态成员变量
class Example {
    static int count;      // 所有对象共享
};

// 3. 静态成员函数
class Example {
    static void func() {   // 不需要对象即可访问
        // 只能访问静态成员
    }
};
```

#### 4.2 const关键字
```cpp
// 常量指针 vs 指针常量
const char* p1;     // 指向常量的指针（指向的值不能改）
char* const p2;     // 常量指针（指针不能改）
const char* const p3; // 指向常量的常量指针（都不能改）

// 常量成员函数
class Example {
    int getValue() const { // 承诺不修改对象的成员
        return value;
    }
};
```

#### 4.3 using关键字
```cpp
// using声明（推荐）
using std::cout;
using std::endl;

// using指令（不推荐，可能污染命名空间）
using namespace std;
```

### 5. 网络编程基础

#### 5.1 Socket编程
```cpp
// 创建socket
int sock = socket(AF_INET, SOCK_STREAM, 0);  // TCP Socket
int sock = socket(AF_INET, SOCK_DGRAM, 0);   // UDP Socket

// 设置地址
struct sockaddr_in addr;
addr.sin_family = AF_INET;
addr.sin_port = htons(8080);
addr.sin_addr.s_addr = INADDR_ANY;

// 基本操作
bind(sock, (struct sockaddr*)&addr, sizeof(addr));  // 绑定
listen(sock, 5);                                    // 监听
accept(sock, nullptr, nullptr);                     // 接受连接
```

### 注意事项

1. 内存管理：
   - 使用智能指针代替裸指针
   - 注意配对使用 new/delete 或 malloc/free
   - 防止内存泄漏

2. 常量使用：
   - 优先使用 const 确保不可修改性
   - 考虑使用 constexpr 进行编译期优化

3. 命名空间：
   - 避免使用 using namespace
   - 优先使用具体的 using 声明

4. 错误处理：
   - 合理使用异常处理
   - 注意资源的正确释放

### 参考资料

- C++ Primer
- Effective C++
- C++ 参考手册
