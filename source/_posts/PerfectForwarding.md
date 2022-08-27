---
title: 完美转发
category:
 - 学习
tag:
- c++
description: c++完美转发

---

##### 1.万能引用

###### 概念

引用只有两种类型：左值引用和右值引用，而万能引用代表该引用要么是左值引用要么是右值引用。即万能引用能够接收左值或右值，并返回左值引用或右值引用。参考[c++万能引用、完美转发](https://blog.csdn.net/lichao201005/article/details/124264766)

###### 使用

1.模板中

```cpp
template<typename T>
void tempFun(T&& t){};//在这里T &&即为万能引用
```

此处注意万能引用和右值引用区别（模板类中）

```cpp
template<typename T>
class A
{
    void fun(T&& t);
    //这里是右值引用，因为T的类型在模板实例化时已经确定，当实例化函数void fun(T&& t);时T的类型已经确定。
}

template<typename T>
class A
{
    template<typename U>
    void func(U&& u);
    //这里是万能引用。细微的差别
}
```

2.auto推断时

```cpp
auto&& var2 = var1;

//引用折叠
int v1 = 100;
//此处发生引用折叠，auto为int，r1类型为int&
auto&& r1 = v1;
//此处没有发生引用折叠，auto为int，r2类型为int &&
auto&& r2 = 200;
```

万能引用与右值引用形式一样，右值引用时确定的类型，如：`int&& ref = x;`

关于引用折叠参考[C++:模板实参推断及引用折叠](https://blog.csdn.net/sixdaycoder/article/details/46489891)

- `X& . & . X& && . X && &`都被折叠为`X&`
- `X&& &&` 折叠为`X &&`

关于auto推断

例：C++ Primer 第五版 练习16.45

```cpp
void g(T&& value)
{
    vector<T> v;
}
int x;
g(42);//解释会发生什么
g(x);//解释会发生什么
```

`g(42)`传入的值为右值，value为int&&，T会被推断为int，

`g(x)`传入的值为左值，value为int&,展开g得到`g(int& &&value)`，引用折叠为`g(int &value)`,其中T仍为`int&`,此时`vector<int&> v`需要初始化。

这里涉及到auto复合用法,参考[C++11新特性之auto类型推导](https://blog.csdn.net/weixin_43340455/article/details/124846389)

```cpp
int t = 4;
auto *t1 = &t; //t1为int*->auto为int
auto t2 = &t;//t2为int*->auto为int*
auto &t3 = t2;//t3为int&->auto为int
auto t4 = t3;//t4为int->auto为int
```

##### 2.完美转发

例：（代码摘自参考文章）

```cpp
template<typename T>
void print(T & t){
    std::cout << "Lvalue ref" << std::endl;
}

template<typename T>
void print(T && t){
    std::cout << "Rvalue ref" << std::endl;
}

template<typename T>
void testForward(T && v){
    print(v);
    print(std::forward<T>(v));
    print(std::move(v));

    std::cout << "======================" << std::endl;
}

int main(int argc, char * argv[])
{
    int x = 1;
    testForward(x);
    testForward(std::move(x));
}
```

在调用`testForward()`时，无论传入参数为左值还是右值，`testForward()`中的`print(v)`方法接收参数为左值，`print(std::move(v))`方法接收参数为右值。实际上是违背开发者原本意图的，所以才会有`print(std::forward<T>(v))`，也即完美转发。，以上代码输出结果如下。

```
Lvalue ref
Lvalue ref
Rvalue ref
======================
Lvalue ref
Rvalue ref
Rvalue ref
======================
```

参考文章：

[[C++][标准库] 完美转发 = 引用折叠 + 万能引用 + std::forward](https://lamforest.github.io/2021/04/29/cpp/wan-mei-zhuan-fa-yin-yong-zhe-die-wan-neng-yin-yong-std-forward/)

[万能引用，引用折叠，移动构造函数，emplace_back及其实现，完美转发及其实现](https://zhuanlan.zhihu.com/p/260508149)

这里有两个小工具：

[GDB online](https://onlinegdb.com/7JwDKZXL9)

[cppinsights](https://cppinsights.io/)可以查看模板实例化的过程
