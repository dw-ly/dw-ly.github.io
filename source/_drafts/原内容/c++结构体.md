---
title: c++结构体相关
date: 2021-09-01 21:57:02
tags:
- c++
categories:
- c++
---

一.c++结构体

1.定义
<!--more-->
```c++
typedef struct Person
{
	int id;
} Per;
```

类似于class功能，不过包含函数默认为public（访问和继承）

参考文章:https://www.cnblogs.com/zhengfa-af/p/8144786.html

二.获取类/结构体中的方法/成员

1."."和"->"

"."为结构运算符，"->"为结构指针运算符

a->b中a为指针，b为成员变量

a.b中a为结构体变量/类对象，b为成员变量

例：

```c++
class student

{

public:       

	string name[20];

}
int main(){
    student *xy = new student;
    //定义student对象指针，访问xy中name属性,以下两种方式等价
    *xy.name = "dwly";
    xy->name = "dwly";
    student xy = new student;
    //定义student类型对象，访问xy中name属性
    xy.name = "dwly";
}
```

2."::"，c++中的双冒号

（1）类作用域，用来标明类的变量、函数。

（2）命名空间作用域，用来标明所使用的类函数属于哪一个命名空间。

（3）全局作用域，局部变量和全局变量同名时，在函数或变量前加::可以调用到全局函数或变量



```c++
// WzSerialPort.h
class WzSerialPort { 
public: // ... 
    bool open(); 
    void close(); // ... 
};
```



```c++
// WzSerialPort.cpp
bool WzSerialPort::open()
{
	if( ::open(portname,O_RDWR|O_NOCTTY|O_NONBLOCK) != -1 )
		return true;
	else
		return false;
}
void WzSerialPort::close()
{
	::close(fd);
}
```

c++中的:: 原文链接：https://blog.csdn.net/qq1623803207/article/details/89398435

三.const关键字

1.作用

（1）可以标明"只读"的**成员函数**，提高程序可读性、可靠性。

（2）给内置的数据类型或自定义类定义一个常量或常量对象。

2.注意

（1）cosnt加到非成员函数或静态成员后面会产生编译错误。

（2）对于mutable修饰的成员，加了const的成员函数可以被非const对象和const对象调用，但不加const的成员函数只能被非const对象调用

```c++
class student
{
public:
    //函数前加const，表示返回值为const
    const int getId();
    //函数后加const，表示函数不可以修改class的成员
    string getName() const;
private:
    string name[20];
    const int id;
}

```



2021年8月31日20:35整理得。
