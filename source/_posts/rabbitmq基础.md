---
title: rabbitmq基础
categories:
 - 消息队列之rabbitmq
description: rabbitmq基础知识总结
---
​       首先，rabbitmq是一种消息队列中间件，由生产者(produce)，消费者(consumer)和rabbitmq服务构成，其中，一个rabbitmq服务的实例称之为brocker,一个brocker又由交换机(exchange)，队列(queue)和他们的绑定关系(bind)组成。一条消息从生产者发送到brocker，再从brocker发送到消费者，是通过生产者和brocker以及消费者之间建立的Tcp连接，但是并不是直接使用某一条Tcp连接(connection)，而是通过在连接上又抽象出来的信道(channel)来进行传输的。信道(channel)是对Tcp连接的复用。

#### brocker组成

​		在brocker中，首先要创建交换机和队列，给创建好的交换机指定**路由类型**，然后将队列绑定到交换机并指定交换机和队列的**绑定关系**(bindingKey)。其中，交换机常用的路由类型有三种，分别是Direct，Fanout以及Topic。Direct会将消息路由到根据routingKey匹配到的一个队列，Fanout会将消息路由到该交换机绑定的所有队列，而Topic会根据一定的匹配规则匹配到任意个数的特定队列并将消息路由过去。
​		关于三种路由规则可以从匹配模式上进一步理解：其实这三者最终结果都是routingKey通过一定规则匹配到队列的bindingKey。Direct是全字符匹配，Fanout是任意匹配，Topic是模糊匹配，其中Topic提供了两个匹配符"\*"和"#"，"\*"只匹配**一个单词**，而"#"可以匹配**任意个单词(包括0和1)**。在rabbitmq中，通过"."来将Key分割为多个单词，例如"test.test"。举个例子，假设brocker上某一个队列的bingdingKey为"A.test"，绑定了该队列的Direct交换机只能通过"A.test"这一routingKey才能路由到该队列，Fanout交换机可以通过任意routingKey路由到该队列,Topic交换机可以设置bindingKey为"\*.test"， "A.\*"，"#.test"，"A.#"等，从而使满足匹配模式的routingKey路由到该队列。例如： bindingKey为"A.\*"时可以路由的routingKey为"A.test"，"A.dev"等，bindingKey为"\*.test"时可以路由的routingKey为"A.test"，"B.test"等。

![direct模式](rabbitmq%E5%9F%BA%E7%A1%80/direct.png)

​																						图1

![fanout模式](rabbitmq%E5%9F%BA%E7%A1%80/fanout.png)

​																						图2

![topic模式](rabbitmq%E5%9F%BA%E7%A1%80/topic.png)

​																					     图3

#### 消息可靠性保证 

​		有了以上机制，生产者通过信道发送消息时携带指定的交换机和routingKey信息,就可以将消息发送到指定的队列上去。但是要想保证消息能够被正确接收，还需要依赖rabbitmq的事务模式或Confirm发送方确认模式。
​		confirm确认模式分为普通确认和批量确认两种模式，当开启confirm普通确认模式时，每个发送到brocker的消息会带有一个唯一的消息编号Tag，下标从1开始，每个信道是独立计数的，在brocker确认某一Tag的消息已收到后,会返回该Tag消息的ack到其发送到brocker的信道。生产者收到该消息的ack就可以确认该消息已到达brocker。(注意：这个确认某一消息收到判断的条件随brocker类型变化而有所不同，例如：当brocker是一个镜像集群，在消息同步到所有需要同步的节点之后brocker才认为消息已收到)批量确认原理与普通确认相同，不过是一次性ack多条消息，比如某一信道发送了1，2，3，4，5五条消息，brocker可能会在收到全部消息后直接对5进行ack，代表前五条消息brocker都已收到。
​		以上两种确认模式在生产者发送消息到brocker和brocker发送到消费者都可以使用，但是在brocker发送到消费者时，消费者收到消息后还需要对消息进行消费，然后再根据消费结果返回ack/nack(手动ack)。以上过程可以理解为同步确认模式，除此之外还可以采用异步消息确认模式，即收到消息就对消息进行ack(自动ack)，然后再对消息进行处理，但是这种模式可能会导致在消息处理失败时，brocker已经将该条消息删除了，从而无法重新消费。(注意：生产者发送到brocker也可以采用异步确认，但是同样有可能由于消息在brocker同步失败导致消息丢失)。另外当开启requeue后，消息消费如果失败，消息会被放回队首重新消费，可能导致无序。



参考：

[RabbitMQ原理详解](https://blog.csdn.net/weixin_43498985/article/details/119026198)

[入门RabbitMQ，这一篇绝对够！](https://mp.weixin.qq.com/s/adse6qpIiK0RE-Ebo-z5_Q)

[透彻rabbitmq](https://zhuanlan.zhihu.com/p/63700605)

其他：

[rabbitmq模拟器](tryrabbitmq.com/)

