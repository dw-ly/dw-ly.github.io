---
title: calculator
date: 2021-09-01 23:57:33
tags:
- c++
categories:
- c++
---

周四开始重新熟悉c++，在了解调用类中方法的时候偶然发现一篇文章关于类的代码组织，在类中定义一个该类的指针和初始化指针的方法，这样在需要调用该类方法的地方使用初始化方法就会得到类的对象，从而调用类中的方法，于是决定写一个程序尝试一下。
<!--more-->

因为没有尝试过，于是打算先写一个简单的只有+-*/的计算器

新建了m_calculator.h、m_calculator.cpp、main.cpp三个文件，将计算器类实现与定义分开，同时在类中定义类的指针，调用初始化方法的时候调用。但是在main.cpp中引用的时候总是报重复定义的问题。代码如下

m_calculator.h

```c++
class Calculator
{
public:
    Calculator(int iNum){m_number = iNum;}
   // ~Calculator();
private:
    int m_number;    
private:
    /* data */
    static Calculator* m_calculator;
public:
    static void InitCalculator();
	//...
    Calculator* Calculator::m_calculator = nullptr;
};
```

尝试很多方法后才知道是m_calculator初始化的问题，m_calculator要在cpp中初始化，并且放在类方法定义之前。正确代码如下：

m_calculator.h

```c++
class Calculator
{
public:
    Calculator(int iNum){m_number = iNum;}
   // ~Calculator();
private:
    int m_number;    
private:
    /* data */
    static Calculator* m_calculator;
public:
    static void InitCalculator();
    static void Add(int number);
    static void Subtract(int number);
    static void Mutiply(int number);
    static void Divide(int number);
    static Calculator* _calculator(){
        return m_calculator;
    }
    static int getNumber() {
        return m_calculator->m_number;
    }
    
};
```

m_calculator.cpp

```c++
#include "m_calculator.h"
//#ifndef Calculator
Calculator* Calculator::m_calculator = nullptr;
void Calculator::Add(int number) {
    Calculator::_calculator()->m_number += number;
}
void Calculator::Subtract(int number) {
    Calculator::_calculator()->m_number -= number;
}
void Calculator::Mutiply(int number) {
    Calculator::_calculator()->m_number *= number;
}
void Calculator::Divide(int number) {
    Calculator::_calculator()->m_number /= number;
}
void Calculator::InitCalculator() {
    Calculator::m_calculator = new Calculator(0);
}
```

后来得知，这种模式将构造函数定义为private时，只能由封装的Init方法进行初始化，且初始化多次只会返回同一个对象，即构成单例模式，具体打算记录在设计模式内容中。
