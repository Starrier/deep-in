---
title: RabbitMQ 原理
date: 2019/2/13
tags: RabbitMQ
index_img: ../post_img/rabbitmq.jpg
---

## RabbitMQ - 原理

![Faced Picture](https://images.pexels.com/photos/1084510/pexels-photo-1084510.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500)

### 生产者消费者模式

### RabbitMQ 概念

 1. ConnectionFactory（连接管理器）：应用程序与Rabbit之间建立连接的管理器，程序代码中使用；
 2. Channel（信道）：消息推送使用的通道；
 3. Exchange（交换器）：用于接受、分配消息；
 4. Queue（队列）：用于存储生产者的消息；
 5. RoutingKey（路由键）：用于把生成者的数据分配到交换器上；
 6. BindingKey（绑定键）：用于把交换器的消息绑定到队列上；

### 创建连接

应用程序和Rabbit Server之间会创建一个TCP连接，一旦TCP打开，并通过了认证，认证就是你试图连接Rabbit之前发送的Rabbit服务器连接信息和用户名和密码，有点像程序连接数据库，使用Java有两种连接认证的方式，后面代码会详细介绍，一旦认证通过你的应用程序和Rabbit就创建了一条AMQP信道（Channel）。

信道是创建在“真实”TCP上的虚拟连接，AMQP命令都是通过信道发送出去的，每个信道都会有一个唯一的ID，不论是发布消息，订阅队列或者介绍消息都是通过信道完成的。

为什么不通过TCP直接发送命令？

对于操作系统来说创建和销毁TCP会话是非常昂贵的开销，假设高峰期每秒有成千上万条连接，每个连接都要创建一条TCP会话，这就造成了TCP连接的巨大浪费，而且操作系统每秒能创建的TCP也是有限的，因此很快就会遇到系统瓶颈。

如果我们每个请求都使用一条TCP连接，既满足了性能的需要，又能确保每个连接的私密性，这就是引入信道概念的原因。

## 消息持久化

RabbitMQ 默认不会进行持久化。

![RabbitMQ picture](https://s5.51cto.com/oss/201710/30/d77e4cfd3d9dc6bf328c7db4b60fbc95.jpg)