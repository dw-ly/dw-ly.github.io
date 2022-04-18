---
typora-copy-images-to:../../images
---

[TOC]



#### 一.rabbitmq

##### 1.结构

![img](../../images/webp.webp)

1）Message
 消息，消息是不具名的，它由消息头和消息体组成。消息体是不透明的，而消息头则由一系列的可选属性组成，这些属性包括routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等。

Publisher
 消息的生产者，也是一个向交换器发布消息的客户端应用程序。

Exchange
 交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列。

Binding
 绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。

Queue
 消息队列，用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走。

Connection
 网络连接，比如一个TCP连接。

Channel
 信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内地虚拟连接，AMQP 命令都是通过信道发出去的，不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接。

![img](../../images/channel.png)

​																      	[图源](https://blog.csdn.net/pmdream/article/details/104780392)

Consumer
 消息的消费者，表示一个从消息队列中取得消息的客户端应用程序。

Virtual Host
 虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。vhost 是 AMQP 概念的基础，必须在连接时指定，RabbitMQ 默认的 vhost 是 / 。

Broker
 表示消息队列服务器实体。包含交换机和队列以及两者的绑定关系。

##### 2.AMQP中消息路由

> AMQP新增了Exchange和Binding。
> 生产者把消息发布到 Exchange 上，消息最终到达队列并被消费者接收，而 Binding 决定交换器的消息应该发送到那个队列。

![img](../../images/producer.png)

##### 3.exchange类型

> Exchange分发消息时根据类型的不同分发策略有区别。
> 目前共四种类型：direct、fanout、topic、headers 。headers 匹配 AMQP 消息的 header 而不是路由键，此外 headers 交换器和 direct 交换器完全一致，但性能差很多，目前几乎用不到了。

###### 3.1 direct

![img](../../images/direct)

消息中的路由键（routing key）如果和 Binding 中的 binding key 一致， 交换器就将消息发到对应的队列中。路由键与队列名完全匹配，如果一个队列绑定到交换机要求路由键为“dog”，则只转发 routing key 标记为“dog”的消息，不会转发“dog.puppy”，也不会转发“dog.guard”等等。它是完全匹配、单播的模式。

###### 3.2 fanout

![img](../../images/fanout)

每个发到 fanout 类型交换器的消息都会分到所有绑定的队列上去。fanout 交换器不处理路由键，只是简单的将队列绑定到交换器上，每个发送到交换器的消息都会被转发到与该交换器绑定的所有队列上。很像子网广播，每台子网内的主机都获得了一份复制的消息。fanout 类型转发消息是最快的。



###### 3.3 topic

![img](../../images/webp-16463651414284.webp)

topic 交换器通过模式匹配分配消息的路由键属性，将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些单词之间用点隔开。它同样也会识别两个通配符：符号“#”和符号“*”。#匹配0个或多个单词，*匹配不多不少一个单词。

参考：[消息队列之 RabbitMQ](https://www.jianshu.com/p/79ca08116d57)

##### 4.消息确认

###### 4.1消息发送确认

场景

> 对于消息发送方来说：
> 生产者发送一条消息，正常情况下是通过交换机发送到队列中，再由消费者接受队列中消息，由消费者进行逻辑操作。
> 但是RabbitMQ在收到消息后，还需要有一段时间才能将消息存入磁盘之中。并且也并不是为每条消息都做fsync的处理，可能仅仅保存到cache中而不是物理磁盘上。
> **如果RabbitMQ broker正巧发生了崩溃，因为消息还没来得及保存磁盘，消息将会丢失。**

成功

> 当消息无法路由到任何队列时，确认消息路由失败。
> 消息成功路由时，当需要发送的队列都发送成功后，进行确认消息，
> 对于持久化队列意味着写入磁盘
> 对于镜像队列意味着所有镜像接收成功

机制

> 事务和publisher confirm(发送者确认)

1)事务

2)publisher confirm(发送者确认)

> 生产者将信道设置成confirm模式，一旦信道进入confirm模式，所有在该信道上面发布的消息都会被指派一个唯一的ID(从1开始)，一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认给生产者（包含消息的唯一ID）,这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会将消息写入磁盘之后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。

总结：confirm模式下，同一信道上每条消息都会有一个唯一的ID（从1开始）消息成功被rabbitmq接收后，broker就会确认该ID的消息ack。可以通过设置multiple来一次性确认多条消息。

判断消息是否被成功接收：（同上`成功`）进入匹配的队列后，持久化消息会在消息写入磁盘后

###### 4.2消息接收确认

> 1、通过 ACK机制（消息确认机制）确认消息是否被正确接收，每个 Message 都要被确认（acknowledged），可以手动去 ACK 或自动 ACK
> 2、默认情况下，一个消息被消费者正确消费就会从队列中移除
> 3、自动确认会在消息发送给消费者后立即确认，但存在丢失消息的可能，如果消费端消费逻辑抛出异常，也就是消费端没有处理成功这条消息，那么就相当于丢失了消息
> 4、如果消息已经被处理，但后续代码抛出异常，使用 Spring 进行管理的话消费端业务逻辑会进行回滚，这也同样造成了实际意义的消息丢失
> 5、如果手动确认则当消费者调用 ack、nack、reject 几种方法进行确认，手动确认可以在业务失败后进行一些操作
> 6、如果某个服务忘记确认 ACK 了，则 RabbitMQ 不会再发送此消息数据给它，只要程序还在运行，没确认的消息就一直是 Unacked 状态，无法被 RabbitMQ 重新投递。
> 7、ACK 机制还可以起到限流作用，比如在接收到某条消息时休眠几秒钟

确认模式

不确认

自动确认

手动确认

参考：[RabbitMQ自学之路（七）—— RabbitMQ消息发送确认与消息接收确认机制](https://blog.csdn.net/qq_29914837/article/details/93376741)

#### 二.部署

##### 1.普通部署

[Ubuntu18.04安装RabbitMQ(正确安装)](https://blog.csdn.net/weixin_33824363/article/details/92254998)

命令

```shell
rabbitmqctl status
rabbitmqctl start_app
rabbitmqctl stop
```

##### 2.docker部署

[用docker部署RabbitMQ环境](https://www.cnblogs.com/yy-cola/p/11089800.html)

命令：

```shell
#启动
docker run -d --hostname my-rabbit --name okong-rabbit rabbitmq:management
#查看运行状态
docker ps
#查看日志
docker logs CONTAINER_ID

```

参考：[在Docker容器中安装部署RabbitMQ](https://blog.csdn.net/weixin_34112030/article/details/91971606)

##### 3.（补充）docker命令

###### 3.1docker删除

```shell
#1.删除镜像
#查看镜像
docker images
#删除镜像，强制删除要加-f
docker rmi id
#2.删除容器
#查看容器
docker ps -a -q
#首先停止容器
docker stop $(docker ps -a -q)
#删除容器
docker rm $(docker ps -a -q)
```

参考：[docker 删除_Docker删除镜像和容器](https://blog.csdn.net/weixin_39969340/article/details/111215044)

###### 3.2.docker进入

```shell
#进入docker
#两种方式
#1.attach 退出时会导致容器停止，不建议使用
sudo docker attach 44fc0f0582d9
#2.exec 
sudo docker -it (name or id) /bin/bash
sudo docker -it (name or id) /bash
```

```shell
#容器停止：docker stop 容器名称
#启动容器：docker start 容器名称
#删除容器：docker rm 容器名称
#删除镜像：docker rmi 镜像名称
#查看运行的所有容器：docker ps
#查看所有容器：docker ps -a
#容器复制文件到物理机：docker cp 容器名称:容器目录 物理机目录
#物理机复制文件到容器：docker cp 物理机目录 容器名称:容器目录
```

参考：[RabbitMQ系列（五）使用Docker部署RabbitMQ集群](https://www.cnblogs.com/vipstone/p/9362388.html)

##### 4.集群

```shell
#添加节点到集群
# node2 处理
sudo docker exec -it rabbit2 /bin/bash
# 加入集群
rabbitmqctl stop_app
rabbitmqctl join_cluster rabbit@rabbit1 #这里要使用 /etc/hosts 中配置的名字
rabbitmqctl start_app
```

参考：[Docker部署RabbitMQ集群](https://zhuanlan.zhihu.com/p/66847766)



##### 5.[docker部署RabbitMQ集群 ](https://www.cnblogs.com/alan6/p/11691229.html)

```shell
#通过docker创建rabbitmq集群
#注意：1.加 -e RABBITMQ_ERLANG_COOKIE='rabbitcookie'配置cookie
#2.加 --link 容器名:主机名 link会配置/var/hosts文件中ip路由情况
#3.启动后需要配置相同erlang cookie
#4.docker logs 容器名称可以查看日志内容取得启动日志的home dir路径
#"/var/lib/rabbitmq/.erlang.cookie"
docker run -d --hostname rabbitmq1 --name node1 -p 15672:15672 -p 5672:5672 -e RABBITMQ_ERLANG_COOKIE='rabbitcookie' rabbitmq:management
docker run -d --hostname rabbitmq2 --name node2 -p 5673:5672 --link node1:rabbitmq1 -e RABBITMQ_ERLANG_COOKIE='rabbitcookie' rabbitmq:management
docker run -d --hostname rabbitmq3 --name node3 -p 5674:5672 --link node1:rabbitmq1 --link node2:rabbitmq2 -e RABBITMQ_ERLANG_COOKIE='rabbitcookie' rabbitmq:management

#配置各个节点组成集群
#主节点1
docker exec -it node1 bash
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl start_app
exit
#从节点2
docker exec -it node2 bash
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl join_cluster --ram rabbit@rabbitmq1
rabbitmqctl start_app
exit
#参数"--ram"表示设置为内存节点，忽略次参数默认为磁盘节点。
#从节点3
docker exec -it myrabbit3 bash
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl join_cluster --ram rabbit@rabbitmq1
rabbitmqctl start_app
exit

#-d 后台进程运行
#hostname RabbitMQ主机名称
#name 容器名称
#-p port:port 本地端口:容器端口
#-p 15672:15672 http访问端口
#-p 5672:5672 amqp访问端口

#给各个节点添加erlang cookie
#容器复制文件到物理机：docker cp 容器名称:容器目录 物理机目录
#物理机复制文件到容器：docker cp 物理机目录 容器名称:容器目录
#设置Erlang Cookie文件权限：“chmod 600 /var/lib/rabbitmq/.erlang.cookie”。

```

参考：

[RabbitMQ系列（五）使用Docker部署RabbitMQ集群](https://www.cnblogs.com/vipstone/p/9362388.html)

[docker部署RabbitMQ集群 ](https://www.cnblogs.com/alan6/p/11691229.html)

转换rabbitmq节点类型

[rabbitmq集群节点操作](https://blog.csdn.net/weixin_30872789/article/details/96524353)

节点退出后重新加入

[RabbitMQ Node rabbit@node1 thinks its clustered with node rabbit@node2, but rabbit disagrees](https://blog.csdn.net/tang_jian_dong/article/details/106587424)

多机器部署

[基于Docker搭建RabbitMQ集群(多台服务器)](http://www.voycn.com/article/jiyudockerdajianrabbitmqjiqunduotaifuwuqi)

#### 三.集群

##### 1.内存节点和磁盘节点

**内存节点**将**元数据**存在**内存**中，**磁盘节点**将**元数据**存在磁盘中。与队列和消息的持久化非持久化没有关系，**队列和消息的持久化就是存磁盘，非持久化就是存内存**。

在持久化队列中,无论 选择哪种节点存储类型, 消息存取 速度没有任何区别; 言下之意，在非持久化队列中, 消息存取会得到很大提升,但是 会出现 重启消息丢失的情况(无论节点存储类型);

因此 在集群**镜像模式**中, 某些非至关重要的队列需求中可以将队列的生产者消费者设置为非持久化,再加上多几个节点进行容错,只要不是所有节点同时宕机，可以得到很好的消息存储速率同时保证消息不丢失。

参考：[rabbitmq集群中 磁盘节点和内存节点区别](https://blog.csdn.net/rgc_520_zyl/article/details/109821061)

##### 2.普通集群和镜像集群

###### 2.1普通集群

> 消息实体在其中一个节点上，消费时同步

​		**消息只存在一个节点上，所有节点都有相同的元数据。消费者无论连接哪一个节点，最终都会到存有消息的节点取消息。**

以两个节点（mq1，mq2）为例说明。

mq1和mq2都有相同的元数据（队列，交换器，绑定，虚主机）。
生产者连接的是mq1节点，消息发送到mq1节点，消费者连接的是mq2，消息从mq2节点进行消费，此时rabbitmq会临时在mq1和mq2之间进行消息传输，将mq1中的消息实体取出并通过mq2发送给消费者。

所以consumer应尽量连接每一个节点，从中取消息。即对于同一个逻辑队列，要在多个节点建立物理Queue。否则无论consumer连01节点或02节点，消息没有在consumer连接的节点上，就需要耗费额外资源进行消息转移，会产生瓶颈。

**节点故障**

当01节点故障后，02节点无法取到01节点中还未消费的消息实体。如果做了消息持久化，那么得等01节点恢复，然后才可被消费；如果没有持久化的话，就会产生消息丢失的现象。

参考：[RabbitMQ自学之路（十）——RabbitMQ知识点整理](https://yilei.blog.csdn.net/article/details/108432410?spm=1001.2014.3001.5502)中的十八.rabbitmq集群方案

**思考**

​		如果流量不大，完全可以让生产者和消费者选取集群中同一个节点分别建立一条连接，同时设置失败保护，连接失败几次后切换节点。生产者消费者连接同一个节点可以减少消息在rabbitmq节点之间转发带来的开销。
​		单条连接情况下，可以通过信道复用单一连接从而节省TCP连接资源，但是当信道本身流量很大的时候多个信道复用同一个连接就会产生性能瓶颈，需要建立多条连接讲信道均摊到多个连接中。可见[RabbitMQ 中 Connection 和 Channel 详解](https://www.cnblogs.com/eleven24/p/10326718.html)中对连接和信道的描述。
​		如果单连接到达生产者消息发送瓶颈，可以建立多条连接到集群的同一个节点，如果到达节点消息处理瓶颈，可以建立多条连接到集群的多个节点，同时消费者也需要连接集群的各个节点。此时可以根据业务拆分发送选取的连接，也可以加一个负载均衡器来选取连接。(**问题**：1.不保序？宏观上讲，是发送到集群broker同一个交换机的绑定的队列上，只是选取了不同的连接发送到不同的节点上，会不会产生顺序错误。2.多线程发送到不同连接是否可行？)

###### 2.2镜像集群

> 消息实体会同步到所有节点上

在可靠性要求较高的场景下使用，内部同步通讯会消耗大量集群内部网络带宽。

实际上也只有主节点（master）对消息进行处理，就算连接到从节点（slave）

镜像队列主拷贝：

> 通过Guarented Multicasting(GM)同步到各个Salve节点，Coodinator负责组播结果的确认。

![img](../../images/rabbitmq_cluster.png)

> GM的主播并不是由Master节点来负责通知所有Slave的（目的是为了避免Master压力过大，同时避免Master失效导致消息无法最终Ack)，RabbitMQ把一个镜像队列的所有节点组成一个链表，由主拷贝发起，由主拷贝最终确认通知到了所有的Slave，而中间由Slave接力的方式进行消息传播。

参考：[深入理解：RabbitMQ的前世今生](https://baijiahao.baidu.com/s?id=1681987279232196884&wfr=spider&for=pc)

###### 2.3其他

2.3.1rabbitmq消息模型

参考：[RabbitMQ六种消息模式](http://www.lzhpo.com/article/9)

#### 四.其他

几种消息队列对比

参考：[一篇文章把RabbitMQ、RocketMQ、Kafka三元归一](http://dockone.io/article/2434606)

备份交换机

参考：[RabbitMQ信道、死信队列进阶](https://zhuanlan.zhihu.com/p/387032657)

客户端连接池

[RabbitMQ客户连接池的实现](https://blog.csdn.net/QQ994406030/article/details/53019678?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-11-53019678.pc_agg_new_rank&utm_term=rabbitmq%E4%B8%BB%E5%8A%A8%E6%96%AD%E5%BC%80%E8%BF%9E%E6%8E%A5&spm=1000.2123.3001.4430)

