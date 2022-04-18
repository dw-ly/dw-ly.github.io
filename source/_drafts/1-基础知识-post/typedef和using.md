---
title: typedef和using
categories: 
 - 1-基础知识
toc: true
description: 关于typedef和using的使用和区别
---
一.typedef

1.类型

​		储存类关键字，不能与auto,extern,mutable,static, register等出现在一个表达式中。

2.用法

1）为现有类型创建别名

```c++
typedef int size
size array
```

2）掩饰复合类型，如指针和数组

```c++
char line[81]
char text[81]

//可以使用typedef
typedef char Line[81]
Line text,line;

typedef char* p;
int　mystrcmp(const　pstr　p1,const　pstr　p3);
```

扩展：

```c++
char* const p //定义一个指向字符的指针常数，即const指针，常量指针。
const char* p //定义一个指向字符型常量的指针。
```

2.typedef与using

```c++
typedef void(*PF)(int, const std::string&);
using PF = void(*)(int,const std::string&);
//二者等价
```

代码简化

```c++
//typedef用法
typedef int (*Register (int (*pf)(const char*, const char*)))
//using用法
using PF = int(*)(const char*, const char*);
PF Register(PF pf);
```

 参考：百度百科[typedef](https://baike.baidu.com/item/typedef/9558154?fr=aladdin)词条

二..using

1.使用

1）命名空间声明

> 我们在书写模块功能时，为了**防止命名冲突会对模块取命名空间**，这样子在使用时就需要指定是哪个命名空间，使用using声明，则后面使用就无须前缀了

例：

```c++
using std::cin;	//using声明，当我们使用cin时，从命名空间std中获取它
int main()
{
	int i;
	cin >> i;	//正确：cin和std::cin含义相同
	cout << i;	//错误：没有对应的using声明，必须使用完整的名字
	std::cout <<i;//正确
    return 0;
}
```

2）在子类中引用基类成员

3）起别名

模板别名：

```c++
//using使用
template <typename T>
using Vec = MyVector<T, MyAlloc<T>>;
 
// usage
Vec<int> vec;
//usage 模板类/参数传递
template <typename T>
class Widget
{
  Vec<T> vec;
};

//typedef使用
template <typename T>
struct Vec
{
  typedef MyVector<T, MyAlloc<T>> type;
};

// usage
Vec<int>::type vec;
//usage 模板类/参数传递
template <typename T>
class Widget
{
  typename Vec<T>::type vec;
};
```

参考：[c++中using的用法](https://blog.csdn.net/weixin_39640298/article/details/84641726)

