---
title: C++ 类完全指南
date: 2024-02-21
author: 第五离央
categories:
  - 编程
  - C++
tags:
  - C++
  - 类
  - 面向对象
---

## C++ 类完全指南

### 1. 类的基本概念

#### 1.1 定义与声明
```cpp
class MyClass {
public:
    // 公有成员
    MyClass();  // 构造函数
    ~MyClass(); // 析构函数
    
protected:
    // 保护成员
    
private:
    // 私有成员
};
```

#### 1.2 访问控制
- `public`: 外部可访问
- `protected`: 仅子类可访问
- `private`: 仅本类可访问
- 默认为 private（区别于 struct 默认为 public）

### 2. 类成员

#### 2.1 成员函数
```cpp
class Example {
public:
    // 普通成员函数
    void normalFunc() {
        // 可以访问类的所有成员
    }
    
    // const 成员函数
    int getValue() const {
        return value;  // 不能修改类成员
    }
    
    // 静态成员函数
    static void staticFunc() {
        // 只能访问静态成员
    }
    
private:
    int value;
};
```

#### 2.2 this 指针
```cpp
class Counter {
public:
    Counter& increment() {
        count++;
        return *this;  // 返回对象自身
    }
    
    // Lambda 中捕获 this
    void lambdaFunc() {
        auto func = [this]() { 
            return this->count; 
        };
    }
    
private:
    int count = 0;
};
```

#### 2.3 内联函数
```cpp
class Math {
public:
    // 类内定义自动成为内联函数
    inline int add(int a, int b) {
        return a + b;
    }
};
```

### 3. 特殊成员函数

#### 3.1 构造函数
```cpp
class Person {
public:
    // 默认构造函数
    Person() = default;
    
    // 带参构造函数
    Person(string name, int age) 
        : name_(name), age_(age) {}  // 初始化列表
    
    // 禁用构造函数
    Person() = delete;
    
private:
    string name_;
    int age_;
};
```

#### 3.2 析构函数
```cpp
class ResourceHandler {
public:
    ~ResourceHandler() {
        // 清理资源
        delete[] buffer_;
    }
private:
    char* buffer_;
};
```

### 4. 友元

#### 4.1 友元函数
```cpp
class Box {
    friend void printBox(const Box& b);  // 友元函数声明
private:
    int width;
};

void printBox(const Box& b) {
    // 可以访问 Box 的私有成员
    cout << b.width << endl;
}
```

#### 4.2 友元类
```cpp
class A {
    friend class B;  // B 是 A 的友元类
private:
    int data;
};

class B {
public:
    void func(A& a) {
        // 可以访问 A 的私有成员
        cout << a.data << endl;
    }
};
```

### 5. 继承与多态

#### 5.1 基本继承
```cpp
class Animal {
public:
    virtual void makeSound() = 0;  // 纯虚函数
protected:
    string name;
};

class Dog : public Animal {
public:
    void makeSound() override {
        cout << "Woof!" << endl;
    }
};
```

#### 5.2 虚继承
```cpp
class A { /*...*/ };
class B : virtual public A { /*...*/ };
class C : virtual public A { /*...*/ };
class D : public B, public C { /*...*/ };  // 避免菱形继承问题
```

#### 5.3 菱形继承
```cpp
// 菱形继承问题
class Base {
protected:
    int data;
public:
    Base(int d) : data(d) {}
};

// 不使用虚继承
class Derived1 : public Base {
public:
    Derived1() : Base(1) {}
};

class Derived2 : public Base {
public:
    Derived2() : Base(2) {}
};

// 会有二义性问题
class Diamond : public Derived1, public Derived2 {
    // data 成员会有两份拷贝
};

// 使用虚继承解决
class VDerived1 : virtual public Base {
public:
    VDerived1() : Base(1) {}
};

class VDerived2 : virtual public Base {
public:
    VDerived2() : Base(2) {}
};

class VDiamond : public VDerived1, public VDerived2 {
    // 只有一份 data 成员
public:
    // 虚继承时最终派生类需要直接调用虚基类的构造函数
    VDiamond() : Base(3), VDerived1(), VDerived2() {}
};
```

#### 5.4 虚函数表和内存布局
```cpp
class Base {
public:
    virtual void func1() {}
    virtual void func2() {}
    int data1;
};

class Derived : public Base {
public:
    virtual void func1() override {}  // 覆盖基类虚函数
    int data2;
};

/*
内存布局：
Base 对象:
    vptr (虚函数表指针) -> Base vtable
    data1

Derived 对象:
    vptr (虚函数表指针) -> Derived vtable
    data1
    data2

虚函数表 (vtable):
Base vtable:
    Base::func1
    Base::func2

Derived vtable:
    Derived::func1  // 覆盖基类func1
    Base::func2     // 继承基类func2
*/
```

#### 5.5 类型转换
```cpp
class Base {
public:
    virtual ~Base() {}
};

class Derived : public Base {
public:
    void derivedFunc() {}
};

void typeCastExample() {
    // 静态转换 - 编译时检查
    Base* base = new Derived();
    Derived* derived1 = static_cast<Derived*>(base);  // 向下转换
    Base* base1 = static_cast<Base*>(derived1);       // 向上转换

    // 动态转换 - 运行时检查
    Derived* derived2 = dynamic_cast<Derived*>(base); // 安全的向下转换
    if (derived2) {
        // 转换成功
        derived2->derivedFunc();
    }

    // const 转换
    const Base* constBase = new Base();
    Base* mutableBase = const_cast<Base*>(constBase);

    // 重新解释转换
    long addr = reinterpret_cast<long>(base);
    Base* ptr = reinterpret_cast<Base*>(addr);
}
```

#### 5.6 多重继承
```cpp
class Interface1 {
public:
    virtual void func1() = 0;
};

class Interface2 {
public:
    virtual void func2() = 0;
};

// 实现多个接口
class Implementation : public Interface1, public Interface2 {
public:
    void func1() override {
        // 实现接口1
    }
    
    void func2() override {
        // 实现接口2
    }
};
```

### 6. 高级特性

#### 6.1 嵌套类
```cpp
class Outer {
public:
    class Inner {
        // 内部类定义
    };
private:
    Inner inner_;
};
```

#### 6.2 运算符重载
```cpp
class Complex {
public:
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, 
                      imag + other.imag);
    }
private:
    double real, imag;
};
```

#### 6.3 CRTP (奇异递归模板模式)
```cpp
template<class Derived>
class Base {
public:
    void interface() {
        // 调用派生类的实现
        static_cast<Derived*>(this)->implementation();
    }
};

class Derived : public Base<Derived> {
public:
    void implementation() {
        // 具体实现
    }
};
```

#### 6.4 成员函数指针
```cpp
class MyClass {
public:
    void func(int x) { std::cout << x << std::endl; }
    int data;
};

void pointerToMemberExample() {
    // 成员函数指针
    void (MyClass::*memFunc)(int) = &MyClass::func;
    
    // 成员变量指针
    int MyClass::*memVar = &MyClass::data;
    
    MyClass obj;
    (obj.*memFunc)(42);    // 调用成员函数
    obj.*memVar = 10;      // 访问成员变量
}
```

### 7. 最佳实践

1. 构造函数：
   - 使用初始化列表
   - 考虑异常安全
   - 避免在构造函数中调用虚函数

2. 资源管理：
   - 遵循 RAII 原则
   - 使用智能指针
   - 实现或禁用拷贝控制

3. 继承：
   - 基类析构函数声明为虚函数
   - override 关键字标记重写函数
   - 使用 final 防止继续继承

### 8. 注意事项

1. 访问控制：
   - 数据成员通常声明为私有
   - 接口函数声明为公有
   - 继承实现声明为保护

2. 性能考虑：
   - 合理使用内联函数
   - 避免不必要的虚函数
   - 注意对象大小和内存对齐

3. 安全性：
   - 防止内存泄漏
   - 处理异常情况
   - 保护类的不变性

### 9. 性能优化

1. 虚函数调用开销：
   - 每次调用需要查找虚函数表
   - 可能破坏指令流水线
   - 编译器难以内联优化

2. 对象布局优化：
   - 考虑内存对齐
   - 合理排列成员变量顺序
   - 利用空基类优化（EBO）

3. 虚继承开销：
   - 额外的内存开销
   - 访问虚基类成员需要间接寻址
   - 构造和析构更复杂

### 10. 调试技巧

1. 虚函数调试：
```cpp
// 添加调试信息
class Base {
public:
    virtual void func() {
        std::cout << "Base::func" << std::endl;
    }
};
```

2. 对象内存布局查看：
```cpp
// 使用 sizeof 和偏移量
std::cout << "Size of Base: " << sizeof(Base) << std::endl;
std::cout << "Offset of data: " << offsetof(Base, data) << std::endl;
```

### 参考资料

- C++ Primer
- Effective C++
- C++ 核心指南
- Inside the C++ Object Model
- Modern C++ Design 