---
created: 2023-06-27 17:07
modified: 2024-07-06 19:03
tags: [消息队列]
---

RabbitMQ 是用 *Erlang* 语言编写的，实现了高级消息队列协议（*AMQP*）的消息中间件。

### AMQP 协议概念

*AMQP* 是一种链接协议，直接定义网络交换的数据格式，这使得实现了 AMQP 的 provider 本身就是跨平台的。以下是 AMQP 协议模型：

![[RabbitMQ 学习.png]]

- `server` - 又称 `broker`，接收客户端的链接，实现 amqp 实体服务
- `connection` - 链接，应用程序跟 broker 的网络链接
- `channel` - 网络信道，几乎所有的操作都是在 channel 中进行，数据的流转都要在 channel 上进行。channel 是进行消息读写的通道。客户端可以建立多个 channel，每个 channel 代表一个会话任务
- `message` - 消息，服务器与应用程序之间传送的数据。由 properties 和 body 组成。properties 可以对消息进行修饰，比如消息的升级，延迟等高级特性。body 就是消息体的内容
- `virtual host` - 虚拟主机，用于进行逻辑隔离，最上层的消息路由，一个虚拟地址里面可以有多个交换机。exchange 和消息队列 message quene
- `exchange` - 交换机，接收消息，根据路由器转发消息到绑定的队列
- `binding` - 绑定，交换机和队列之间的虚拟链接，绑定中可以包含 routing key
- `routing key` - 一个路由规则，虚拟机可以用它来确定如何路由一个特定消息
- `quene` - 消息队列，保存消息并将它们转发给消费者

### RabbitMQ 的消息模型

#### 1. 简单模型

![[RabbitMQ 学习-1.png|375]]

- P：生产者
- C：消费者
- 红色部分：quene，消息队列

#### 2. 工作模型

![[RabbitMQ 学习-2.png|394]]

当消息处理比较耗时时，就会出现生产消息的速度远远大于消费消息的速度，这样就会出现消息堆积，无法及时处理。这时就可以让多个消费者绑定一个队列，去消费消息，队列中的消息一旦消费就会丢失，因此任务不会重复执行。

- P：生产者
- C 1、C 2：消费者
- 红色部分：quene，消息队列

#### 3. 广播模型（fanout）

![[RabbitMQ 学习-3.png|400]]

这种模型中生产者发送的消息所有消费者都可以消费。

- P：生产者
- X：交换机
- C 1、C 2：消费者
- 红色部分：quene，消息队列

#### 4. 路由模型（routing）

![[RabbitMQ 学习-4.png|475]]

这种模型消费者发送的消息，不同类型的消息可以由不同的消费者去消费。

- P：生产者
- X：交换机，接收到生产者的消息后将消息投递给与 routing key 完全匹配的队列
- C 1、C 2：消费者
- 红色部分：quene，消息队列

#### 5. 订阅模型（topic）

![[RabbitMQ 学习-5.png|475]]

这种模型和 direct 模型一样，都是可以根据 routing key 将消息路由到不同的队列，只不过这种模型可以让队列绑定 routing key 的时候使用通配符。这种类型的 routing key 都是由一个或多个单词组成，多个单词之间用.分割。

通配符介绍：
- `*`：只匹配一个单词
- `#`：匹配一个或多个单词

#### 6. RPC 模型

![[RabbitMQ 学习-6.png|600]]

这种模式需要通知远程计算机运行功能并等待返回运行结果。这个过程是阻塞的。

当客户端启动时，它创建一个匿名独占回调队列。并提供名字为 call 的函数，这个 call 会发送 RPC 请求并且阻塞直到收到 RPC 运算的结果。

> [!link]- Reference
> 
> - [千锋 RabbitMQ 入门](https://www.bilibili.com/video/BV1Qv411B7WS)
> - [消息中间件 - RabbitMQ](https://www.xuebin.work/archives/rabbitmq)
> - [Springboot 整合 RabbitMq ，用心看完这一篇就够了](https://blog.csdn.net/qq_35387940/article/details/100514134)
> - [spring-boot-route（十三）整合 RabbitMQ](https://blog.csdn.net/m0_37968982/article/details/109023177)
> - [没用过消息队列？一文带你体验 RabbitMQ 收发消息](https://segmentfault.com/a/1190000023473302)
> - [刚体验完 RabbitMQ？一文带你 SpringBoot+RabbitMQ 方式收发消息](https://segmentfault.com/a/1190000023564860#item-4)
> - [上手了 RabbitMQ？再来看看它的交换机(Exchange)吧](https://segmentfault.com/a/1190000023684246)
> - [RabbitMQ 高级之如何保证消息可靠性](https://segmentfault.com/a/1190000023736395)
> - [RabbitMQ 高级之消息限流与延时队列](https://segmentfault.com/a/1190000023859065)
> - [RocketMQ 实战](https://apppukyptrl1086.pc.xiaoe-tech.com/detail/p_5d887e7ea3adc_KDm4nxCm/6)
