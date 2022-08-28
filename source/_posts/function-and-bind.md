---
title: function和bind
category:
 - 学习笔记
tag:
 - c++
description: function和bind


---

#### 一.std::function

##### 1.定义

​        std :: function是一个通用的多态函数包装器。 

##### 2.使用

​        std :: function的实例可以**存储，复制和调用**任何可调用的目标 ：包括函数，lambda表达式，绑定表达式或其他函数对象，以及指向成员函数和指向数据成员的指针。

##### 3.注意

​        当std::function对象未包裹任何实际的可调用元素，调用该std::function对象将抛出std::bad_function_call异常。

参考：[C++11 function类模板](https://blog.csdn.net/qq_35721743/article/details/83217416)

#### 二.std::bind和std::function

```cpp
void test(int a, int b, int c)
{
    printf("-------\n%d\n%d\n%d\n-------\n",a,b,c);
}
class T
{
private:
    /* data */
public:
    T(/* args */) = default;
    ~T() = default;
    void test(){printf("test\n");};
};
```

##### 1.std::function

保存调用模式，用统一的方式处理 函数，函数指针，函数对象。

```cpp
function<void()> func;
void test(){printf("123");}
//绑定普通函数
func = test;
class T
{
    void test(){printf("123");};
};
//绑定类c成员方法
function<void(T&)> func2;
T t;
func2 = &T::test;
func2(t)
//或者
function<void(T*)> func2;
T t;
func2 = &T::test;
func2(&t);
/*
    实际上两种写法体现出类成员方法会在第一个参数隐式传入类指针，
    两种写法中分别使用了类引用和类指针。
    对于赋值时的&T::test见参考
*/
```

赋值时使用&T::test原因参考：[invalid+use+of+non-static+member+function+问题_i丹明扬的博客-CSDN博客](https://blog.csdn.net/bill_ming/article/details/6872165) 

##### 2.std::bind

可以理解为将不同输入输出的函数可以通过bind按照绑定规则绑定到同一个函数指针上。统一入口。举个例子，有一个function为`function<void()> test`有一个方法是`void tmp(int a,int b)`有两个入参，可以通过bind将tmp绑定到test上并且传入两个数字1和2作为tmp方法的入参:`test = bind(tmp,1，2)`甚至可以指定传入的参数：

`test = bind(tmp,a,b)`但是不能使用占位符`placeholders`因为模板里没有可以传入的参数位置。

```cpp
//使用bind绑定
function<void(int,int)> func;
//绑定普通函数
func = bind(test,placeholders::_2,placeholders::_1, 100);
func(3,4);
//绑定lambda函数
func = bind([](int a, int b, int c, int d){printf("lambda(%d,%d)\n-------\n",a+c,b+d);},placeholders::_1,placeholders::_2,3,4);
func(10,9);
T tt;
//绑定类成员方法
func = bind(&T::test,&tt);
func(10,9);
//这里绑定的类成员方法没有入参，bind时不加占位符。这样可以将参数个数少的方法绑定到参数
//个数多的函数指针上
```

`std::function`可以绑定的函数包括

- 普通函数
- lambda表达式
- 函数指针
- 仿函数(functor 重载括号运算符实现)
- 类成员函数
- 静态成员函数

参考：[C++ 之std::function()及 std::bind() 学习总结_ppipp1109的博客-CSDN博客](https://blog.csdn.net/p942005405/article/details/84760715)

##### 3.回调函数

把调用者和被调用者分开，调用者不用关心被调用者是谁。


