---
# typora-copy-images-to:../../images
title: 网络IO
category:
 - 学习
tag:
 - 网络IO
description: 从socket到reactor
---

​		socket可以理解为tcp/ip的连接通信过程的抽象，在S/C架构中，client和server的通信就是使用的socket，在经典socket通信模型中，client只需要开一个线程连接server即可，但是server需要处理多个client的连接，每一个连接都会开启一个线程，虽然线程消耗比进程小，但是每台机器实际能够建立的线程数还是有限的，并且随着线程数量的增加，在超过某一个阈值之后性能会开始下降（**测试一下**）
​		IO，也就是input/output，涉及到数据的存储和传输就需要进行IO操作，IO操作主要有磁盘IO和网络IO，这里主要讨论网络IO，对于一个IO操作来说，实际上分为两个步骤，发起IO请求和实际的IO操作。根据发起IO请求是否会被阻塞直到数据返回分为阻塞IO和非阻塞IO，根据实际IO操作中用户进程是否需要等待内核数据(阻塞)可以分为同步IO和异步IO(其中，异步IO在数据完成并复制到用户空间之后会产生一个信号或者执行一个基于线程的回调函数来完成本次IO的处理)。由此可以将阻塞/非阻塞和同步/异步的区别理解为：阻塞/非阻塞区别在于IO操作的第一步是否阻塞，同步/异步区别在于IO操作的第二步是否阻塞。实际上，上文提到的经典的socket通信模型是同步阻塞IO。因为IO操作两个步骤都是阻塞的。
​		阻塞IO(blicking IO)和非阻塞IO(nonblocking IO)

![img](%E4%BB%8Esocket%E5%88%B0reactor%E6%A8%A1%E5%9E%8B/BIO.webp)

​																				BIO

![img](%E4%BB%8Esocket%E5%88%B0reactor%E6%A8%A1%E5%9E%8B/NIO.webp)

​																				NIO

​		对于非阻塞IO来说，由于不知道数据是否发送完成，会产生半包现象，通常需要自行处理，一般采用以换行符作为判断依据，定长消息发送，或者自定义协议等(如定义数据包为包头和包体，在包头中加入包体长度参数对数据包长度进行标识)。
​		reactor模型，即反应堆模型。主要有连接器，事件分发器、消息处理器组成，连接器接收client的连接，建立对应的消息处理器并将其注册到事件分发器上，事件分发器是绑定在一个线程上的一个无限事件循环，会在某一个事件发生的时候回调预先定义好的回调函数(消息处理器)来进行事件的处理。其中事件分发器可以使用的分发机制有select、poll和epoll。

参考：

[网络IO-概述](https://segmentfault.com/a/1190000041328675)

[高性能Server---Reactor模型](https://www.jianshu.com/p/2461535c38f3)

[同步IO/异步IO, 阻塞IO/非阻塞IO](https://www.jianshu.com/p/490efa0d4ef2)

