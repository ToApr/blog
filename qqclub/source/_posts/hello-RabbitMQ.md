---
title: 初始RabbitMQ
date: 2019-11-05 16:43:23
tags: rabbitmq
categories: 技术
comments: true
---

# 前言
## 1.什么是消息？
   消息(Message) 是指在应用间传送的数据。消息可以非常简单，比如只包含文本字符串、JSON 等，也可以很复杂，比如内嵌对象。
## 2. 什么是消息队列中间件
   消息队列中间件(Message Queue Middleware 简称为MQ) 是指利用高效可靠的消息传递机制进行与平台无关的数据交流，并基于数据通信来进行分布式系统的集成。通过提供消息传递和消息排队模型，它可以在分布式环境下扩展进程间的通信。
## 3.为什么要用消息队列，消息中间件的作用是什么?
  解耦，冗余，扩展性，削峰，可恢复性，顺序性，缓冲，异步通信
# 实战安装
## 1.rabibtmq 安装步骤（windows）
  添加环境变量 RABBITMQ_BASE。（可不做）
  安装向导链接：https://www.rabbitmq.com/install-windows.html
  安装 Erlang  ，安装rabbitmq server.
 Erlang 相当.framwork ,有了这玩意才能安装运行 rabbitmq server
 傻瓜式安装。  

 Erlang 20.2 or later 


|Erlang  Cookie 位置|对应位置|作用|
|-------|----|-------------------------|
| %HOMEDRIVE%%HOMEPATH%\.erlang.cookie| C:\Users\%USERNAME%\.erlang.cookie| 不详,可能 CLI tools |
| %USERPROFILE%\.erlang.cookie | C:\WINDOWS\system32\config\systemprofile  | for the RabbitMQ Windows service |
## 2.rabitmq日志，消息DB目录 
|配置项|默认目录|
|---------------------|--------------|
|RABBITMQ_BASE|	％APPDATA％ \ RabbitMQ|
|RABBITMQ_CONFIG_FILE|	％RABBITMQ_BASE％ \ rabbitmq|
|RABBITMQ_MNESIA_BASE|	％RABBITMQ_BASE％ \ db|
|RABBITMQ_MNESIA_DIR|	％RABBITMQ_MNESIA_BASE％ \ ％RABBITMQ_NODENAME％|
|RABBITMQ_LOG_BASE	|％RABBITMQ_BASE％ \ log|
|RABBITMQ_LOGS	|％RABBITMQ_LOG_BASE％ \ ％RABBITMQ_NODENAME％ .log|
|RABBITMQ_PLUGINS_DIR|	安装目录/插件|
|RABBITMQ_PLUGINS_EXPAND_DIR|	％RABBITMQ_MNESIA_BASE％ \ ％RABBITMQ_NODENAME％ -plugins-expand|
|RABBITMQ_ENABLED_PLUGINS_FILE|	％RABBITMQ_BASE％ \ enabled_plugins|
|RABBITMQ_CONFIG_FILE|配置文件目录指定(advanced.config)|

## 3.常用管理命令
|命令|作用|
|---------------------------|-------------------------|
| rabbitmq-service install||
|rabbitmq-service enable||
|rabbitmq-service start||
|rabbitmqctl status||
|rabbitmqctl list_users|查看用户列表|
|rabbitmqctl add_user liufei liufei|添加用户|
|rabbitmqctl set_user_tags liufei administrator|分配用户权限|
|rabbitmq-plugins list | 查看插件列表|
|rabbitmq-plugins enable rabbitmq_management|  启用Web管理界面插件|
## 4.4.	http://localhost:15672  查看管理界面

# 开始使用

我们先来看一个RabbitMQ的运转流程，稍后会对这个流程中所涉及到的一些概念进行详细的解释。
## 1.生产者
   1. 生产者连接到RabbitMQ Broker，建立一个连接(Connection)开启一个信道(Channel)
2. 生产者声明一个交换器，并设置相关属性，比如交换机类型、是否持久化等
3. 生产者声明一个队列井设置相关属性，比如是否排他、是否持久化、是否自动删除等
4. 生产者通过路由键将交换器和队列绑定起来
5. 生产者发送消息至RabbitMQ Broker，其中包含路由键、交换器等信息
6. 相应的交换器根据接收到的路由键查找相匹配的队列
7. 如果找到，则将从生产者发送过来的消息存入相应的队列中
8. 如果没有找到，则根据生产者配置的属性选择丢弃还是回退给生产者
9. 关闭信道
10. 关闭连接
## 2.消费者
 1. 消费者连接到RabbitMQ Broker ，建立一个连接(Connection)，开启一个信道(Channel) 
 2. 消费者向RabbitMQ Broker请求消费相应队列中的消息，可能会设置相应的回调函数
3. 等待RabbitMQ Broker回应并投递相应队列中的消息，消费者接收消息
4. 消费者确认(ack) 接收到的消息
5. RabbitMQ从队列中删除相应己经被确认的消息
6. 关闭信道
7. 关闭连接
## 基本概念
  **队列、交换器、路由key、绑定**

从RabbitMQ的运转流程我们可以知道生产者的消息是发布到交换器上的。而消费者则是从队列上获取消息的。那么消息到底是如何从交换器到队列的呢？我们先具体了解一下这几个概念

 ***Queue***：队列，是RabbitMQ的内部对象，用于存储消息。RabbitMQ中消息只能存储在队列中，生产者投递消息到队列，消费者从队列中获取消息并消费。多个消费者可以订阅同一个队列，这时队列中的消息会被平均分摊(轮询)给多个消费者进行消费，而不是每个消费者都收到所有的消息进行消费。

注意：RabbitMQ不支持队列层面的广播消费，如果需要广播消费，可以采用一个交换器通过路由Key绑定多个队列，由多个消费者来订阅这些队列的方式。

 ***Exchange***:交换器。在RabbitMQ中，生产者并非直接将消息投递到队列中。真实情况是，生产者将消息发送到Exchange(交换器)，由交换器将消息路由到一个或多个队列中。如果路由不到，或返回给生产者，或直接丢弃，或做其它处理。

***RoutingKey***:路由Key。生产者将消息发送给交换器的时候，一般会指定一个RoutingKey，用来指定这个消息的路由规则。这个路由Key需要与交换器类型和绑定键(BindingKey)联合使用才能最终生效。在交换器类型和绑定键固定的情况下，生产者可以在发送消息给交换器时通过指定RoutingKey来决定消息流向哪里。

***Binding***:RabbitMQ通过绑定将交换器和队列关联起来，在绑定的时候一般会指定一个绑定键，这样RabbitMQ就可以指定如何正确的路由到队列了。从这里我们可以看到在RabbitMQ中交换器和队列实际上可以是一对多，也可以是多对多关系。交换器和队列就像我们关系数据库中的两张表。它们同归BindingKey做关联(多对多关系表)。在我们投递消息时，可以通过Exchange和RoutingKey(对应BindingKey)就可以找到相对应的队列。
# RabbitMQ主要有四种类型的交换器：
* ***fanout***:扇形交换器，它会把发送到该交换器的消息路由到所有与该交换器绑定的队列中。如果使用扇形交换器，则不会匹配路由Key
![fanout](/images/fanout.png)

*  ***direct***:direct交换器，会把消息路由到RoutingKey与BindingKey完全匹配的队列中。
![direct](/images//direct.png)
*  ***topic***:完全匹配BindingKey和RoutingKey的direct交换器有些时候并不能满足实际业务的需求。topic类型的交换器在匹配规则上进行了扩展，它与direct类型的交换器相似，也是将消息路由到BindingKey和RoutingKey相匹配的队列中，但这里的匹配规则有些不同，它约定•	RoutingKey为一个点号"."分隔的字符串(被点号"."分隔开的每一段独立的字符串称为一个单词)，如"hs.rabbitmq.client"，"com.rabbit.client"等。
BindingKey和RoutingKey一样也是点号"."分隔的字符串；
BindingKey中可以存在两种特殊字符串"*"和"#"，用于做模糊匹配，其中"*"用于匹配一个单词，"#"用于匹配多规格单词(可以是零个)
![topic](/images/topic.png)
* ***header***不常用
# 代码
客户端API向导：https://www.rabbitmq.com/dotnet-api-guide.html
# RPC调用

RPC ， 是Remote Procedure Call 的简称，即远程过程调用。它是一种通过网络从远程计算
机上请求服务，而不需要了解底层网络的技术。RPC 的主要功用是让构建分布式计算更容易，在提供强大的远程调用能力时不损失本地调用的语义简洁性。通俗点来说，假设有两台服务器A 和B ， 一个应用部署在A 服务器上，想要调用B 服务器上应用提供的函数或者方法，由于不在同一个内存空间， 不能直接调用，需要通过网络来表达调用的语义和传达调用的数据。

![rpc](/images/rpc.png)

## RPC 的处理流程如下
1. 当客户端启动时，创建一个匿名的回调队列(名称由RabbitMQ 自动创建，图4-7 中的回调队列为amq.gen-LhQzlgv3GhDOv8PIDabOXA ) 。
2. 客户端为RPC 请求设置2 个属性: replyTo 用来告知RPC 服务端回复请求时的目的队列，即回调队列; correlationld 用来标记一个请求。
3. 请求被发送到rpc_queue 队列中。
4. RPC 服务端监听rpc_queue 队列中的请求，当请求到来时， 服务端会处理并且把带有结果的消息发送给客户端。接收的队列就是replyTo 设定的回调队列。
5. 客户端监昕回调队列， 当有消息时， 检查correlationld 属性，如果与请求匹配，那就是结果了。
###  什么是备用交换机  
 备用交换机是用来处理消息生产者，投递到指定交换机中不可被正确投递的消息，进入备用交换机。  
### 什么是死信交换机  
 死信交换机是处理 过期消息或者被消费者丢弃的消息，会被路由到死信交换机。  

***参考引用：https://mp.weixin.qq.com/s/9_OQaK2p5hiSgy4IYjvR5w  
Rabbitmq实战指南  朱忠华 著***

