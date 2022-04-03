---
typora-copy-images-to:../../images
---

[TOC]

#### 一.计算机储存结构

金字塔结构中越靠近塔尖速度更快，成本更高

![ad7f67473e7c2abcd43825f677a6899a.png](../../images/ad7f67473e7c2abcd43825f677a6899a.png)

#### 二.储存单元和寄存器变量

```c
// 写入到dest，从src读入 
void write_read(long *src, long *dst, long n) 
{ 
    long cnt = n; 
    long val = 0; 
    while (cnt) { 
	    // 写内存, 把val的变量值，写入到dst指针指向的内存空间 
        *dst = val; 
        // 读内存, 读取src指针指向的内存空间，并且加1后保存到变量val 
        val = (*src) + 1; 
        cnt--; 
    } 
}

```

数组a[-10,17]
例1：write_read(&a[0], &a[1]),3);

观察while执行发现第三次迭代相比第二次迭代，不会对数组a的数据的产生变动。于是第3次迭代的过程就没有必要去写内存（不执行Store操作）

CPU具体实现方式：使用存储单元(Store Unit)。存储单元每个缓冲区,都包括了将要写入到内存的数据，数据结构由内存地址和具体的数据构成。我们用一对括号表示(内存地址，数据)。

![img](../../images/v2-113d6c45bf12d7eb05270031fa475a95_720w.jpg)

例2：write_read(&a[0], &a[0]),3);

调用函数的前两个参数引用均为a[0]，每次迭代都会改变数组a[0]的值，每次都会对内存进行先写后读。

> CPU没办法对这种进行情况就行优化，而且因为指针*dest和*src指向同一个内存位置。**读写的地址相同，所以每次读的操作，都必须等待上一条写(Load操作)完成**。这种特殊的现象，我们称之为**写/读相关(write/read dependency)**。对于完全支持流水线（pipeline)技术的CPU而言，这种等待, 会造成浪费时间。而前面的示例1就没有这种等待，它们的指令可以进入流水线并行。

也就是读写同一地址时，读操作需要等待写操作完成，导致无法并行执行。

```c
void psum1(float a[], float p[], long n) 
{ 
    long i; 
    p[0] = a[0]; 
    for (i = 1; i < n; i++) 
        p[i] = p[i-1]+a[i]; 
}
```

```c
void psum2(float a[], float p[], long n) 
{ 
    long i; 
    p[0] = a[0]; 
    float last_val = p[0] = a[0]; 
    for (i = 1; i < n; i++) 
        val =last_val+a[i]; p[i] = val; last_val = val; 
}
```

相比psum1，psum2 在上一次循环中记忆了p[i]的值，保存在last_val变量中，留给下个迭代使用，避免了Load操作。**局部变量使用了寄存器，而不会去读内存，提高性能。**

参考文章：[理解CPU的Load和Store操作](https://zhuanlan.zhihu.com/p/443474348)

参考文献：《CS:APP》



```c++
#include <iostream>
#include <time.h>
using namespace std;
void func1();
void func2();
int main()
{
        clock_t startTime,endTime;
        startTime = clock();//计时开始
        func1();
        endTime = clock();//计时结束
        cout << "The func1 run time is: " <<(double)(endTime - startTime) / CLOCKS_PER_SEC << "s" << endl;
        startTime = clock();//计时开始
        func2();
        endTime = clock();//计时结束
        cout << "The func2 run time is: " <<(double)(endTime - startTime) / CLOCKS_PER_SEC << "s" << endl;
        system("pause");
        return 0;
}

void func1()
{
        register int temp, i;
        int p=0;
        for ( i=0; i<=30000; i++ )
                for ( temp=0; temp<=100; temp++ );
        //              p++;
}
void func2()
{
        int temp, i;
        int p=0;
        for ( i=0; i<=30000; i++ )
                for ( temp=0; temp<=100; temp++ ) ;
          //              p++;

}

```

func函数中对p变量执行++操作时会对cpu执行load操作

执行时间如下：

![image-20220218100919404](../../images/image-20220218100919404.png)

注释掉p++后执行时间如下：

![image-20220218100706661](../../images/image-20220218100706661.png)

#### 三.缓存

##### 1.定义

> 高速缓存是一个小而快速的存储设备 ，它作为存储在更大更慢的设 备中的数据对象的缓冲区域。使用高速缓存的过程称为缓存 。

##### 2.出现原因

> 缓存又叫高速缓存，是计算机存储器中的一种，本质上和硬盘是一样的，都是用来存储数据和指令的 。它们最大的区别在于读取速度的不同。
> 程序一般是放在内存中的，当CPU执行程序的时候，执行完一条指令需要从内存中读取下一条指令，读取内存中的指令要花费100000个时钟周期（缓存读取速度为200个时钟周期，相差500倍）
> 于是，有人就提出了这样一种方法，在CPU和内存之间添加一个高速内存， 这个高速内存容量小，只用来存储CPU执行时常用的指令。既保证了硬件成本，又提高了CPU的访问速度。这个高速内存就是缓存（高速缓存）。



##### 3.分类

> CPU缓存有**多级缓存**，比如L1, L2, L3等：
> L1容量最小，速度最快，**每个核都有L1缓存**，L1又专门针对指令和数据分成**L1d(数据缓存)**,**L1i(指令缓存)**。
> L2容量比L1大，速度比L1慢，**每个核都有L2缓存**。
> L3容量最大，速度最慢，**多个核共享一个L3缓存**。

![å¨è¿éæå¥å¾çæè¿°](../../images/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzUxNDM3,size_16,color_FFFFFF,t_70.png)

> 下图所示为 Intel Core i7处理器的高速缓存层次结构。每个CPU芯片有四个核。每个核有自己的L1 i-cache， L1 d-cache和L2统一的高速缓存。所有的核共享片上L3统一的高速缓存。其具体参数如下表所示。

![9420faa5b7dea1274b95ec32af5a5b1a.png](../../images/cache2.png)

参考：[c++ 缓存和缓冲_24张图7000字详解计算机中的高速缓存](https://blog.csdn.net/weixin_35748962/article/details/113048716?spm=1001.2101.3001.6650.13&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-13.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-13.pc_relevant_default&utm_relevant_index=20)

4.cpu访问

> cpu从来都不直接访问内存, 都是通过cache间接访问内存，每次访问内存时先查看cache中是否有对应地址的数据，如果cache中没有则先分配一个cache entry，然后再把内存中的数据copy到刚刚分配的cache entry的cache line中，再从cache line中读取数据。

![](../../images/cache.png)

5.扩展

优化

> 分块的大小与cpu cache line关系？
> 如果我们访问内存时，先把数据读取到CPU缓存cache line再计算，而下次读取到该数据时直接使用缓存（若未被淘汰掉），修改cpu cache line这个数值,数据字节对齐可能决定一次操作接触1个还是2个缓存行

> 避免伪共享的问题？
> 当多线程修改互相独立的变量（一般出现在全局变量或者一些动态申请的内存）时，如果这些变量共享同一个缓存行，那么当某个核修改缓存行某个变量值时，需要锁定缓存行，同时其他核已经加载该缓存行的cache也跟着失效，需要重新从内存读取。提到 cache line 往往会提到伪共享，也就是多核 CPU 的多线程编程中，每个线程虽然都访问自己本地的变量，没有使用任何线程同步锁，但由于这些变量在一个 cache line 上，也会造成 cache miss，导致性能严重下降。可以通过内存对齐使多线程之间不共享 cache line，以达到优化的目的。其中一个解决思路，就是让不同线程操作的对象处于不同的缓存行即可。

参考:[c++缓存优化](https://blog.csdn.net/qq_39751437/article/details/106373306?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~HighlightScore-5.queryctrv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~HighlightScore-5.queryctrv2&utm_relevant_index=8)

6.缓存一致性（MESI协议）



参考：[【并发编程】CPU cache结构和缓存一致性（MESI协议）](https://blog.csdn.net/reliveIT/article/details/50450136)



必读经典文章：[与程序员相关的CPU缓存知识](https://coolshell.cn/articles/20793.html)

四.CPU缓存

1.cache line

CPU一次性加载的数据大小

> 比如：Cache Line是最小单位（64Bytes），所以先把Cache分布多个Cache Line，比如：L1有32KB，那么，32KB/64B = 512 个 Cache Line。

2.CPU Associativity

CPU cache放置策略

> 因为Cache的大小远远小于内存，所以，需要有一种地址关联的算法，能够让内存中的数据可以被映射到Cache中来。

3.N-Way关联

> 把连续的N个Cache Line绑成一组，然后，先把找到相关的组，然后再在这个组内找到相关的Cache Line。

> 32KB的可以分成，32KB / 64 = 512 条 Cache Line。
> 因为有8 Way，于是会每一Way 有 512 / 8 = 64 条 Cache Line。
> 于是每一路就有 64 x 64 = 4096 Byts 的内存。

4.索引内存地址

> **Tag**：每条 Cache Line 前都会有一个独立分配的 24 bits来存的 tag，其就是内存地址的前24bits
> **Index**：内存地址后续的6个bits则是在这一Way的是Cache Line 索引，2^6 = 64 刚好可以索引64条Cache Line
> **Offset**：再往后的6bits用于表示在Cache Line 里的偏移量.

![img](../../images/L1CacheExample.png)

如图：横向位8个way，每个way有纵向所示64条cacheline，每条cacheline的结构如上所述。

当CPU访问某一个内存的时候，先通过中间6bit **index**来定位哪一个set（理解：8路关联时8条cacheline为一组，中间6bit索引组号），通过前24bit定位相应cacheline。

另：处理器位数指的是一个时钟周期内，处理器处理的[二进制代码数](https://zhidao.baidu.com/question/1775020547399362780.html)。

> 所谓32位处理器就是一次只能处理32位，也就是4个字节的数据，而64位处理器一次就能处理64位，即8个字节的数据。如果我们将总长128位的指令分别按照16位、32位、64位为单位进行编辑的话：旧的16位处理器，比如Intel 80286 CPU需要8个指令，32位的处理器需要4个指令，而64位处理器则只要两个指令，显然，在工作频率相同的情况下，64位处理器的处理速度会比16位、32位的更快。

5.缓存一致性

[CPU缓存演示](https://www.scss.tcd.ie/Jeremy.Jones/VivioJS/caches/MESIHelp.htm)

6.示例程序

#### 四.缓存与内存



**64位系统默认对齐为8位**

**32位系统默认对齐为4位**

