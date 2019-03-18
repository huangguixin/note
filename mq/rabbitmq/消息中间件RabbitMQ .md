# 消息中间件RabbitMQ 

## 1 RabbitMQ简介 

### 1.1消息队列中间件简介 

消息队列中间件是分布式系统中重要的组件，主要解决应用耦合，异步消息，流量削锋等问题实现高性能，高可用，可伸缩和最终一致性[架构] 使用较多的消息队列有ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ以下介绍消息队列在实际应用中常用的使用场景：异步处理，应用解耦，流量削锋和消息通讯四个场景 

## 1.2 什么是RabbitMQ 

RabbitMQ 是一个由 Erlang 语言开发的 AMQP 的开源实现。
AMQP ：Advanced Message Queue，高级消息队列协议。它是应用层协议的一个开放
标准，为面向消息的中间件设计，基于此协议的客户端与消息中间件可传递消息，并不
受产品、开发语言等条件的限制。
RabbitMQ 最初起源于金融系统，用于在分布式系统中存储转发消息，在易用性、扩展
性、高可用性等方面表现不俗。具体特点包括：
1.可靠性（Reliability）
RabbitMQ 使用一些机制来保证可靠性，如持久化、传输确认、发布确认。
2.灵活的路由（Flexible Routing）
北京市昌平区建材城西路金燕龙办公楼一层 电话：400-618-9090
在消息进入队列之前，通过 Exchange 来路由消息的。对于典型的路由功能，RabbitMQ
已经提供了一些内置的 Exchange 来实现。针对更复杂的路由功能，可以将多个
Exchange 绑定在一起，也通过插件机制实现自己的 Exchange 。 

3.消息集群（Clustering）
多个 RabbitMQ 服务器可以组成一个集群，形成一个逻辑 Broker 。
4.高可用（Highly Available Queues）
队列可以在集群中的机器上进行镜像，使得在部分节点出问题的情况下队列仍然可用。
5.多种协议（Multi-protocol）
RabbitMQ 支持多种消息队列协议，比如 STOMP、MQTT 等等。
6.多语言客户端（Many Clients）
RabbitMQ 几乎支持所有常用语言，比如 Java、.NET、Ruby 等等。 

7.管理界面（Management UI）
RabbitMQ 提供了一个易用的用户界面，使得用户可以监控和管理消息 Broker 的许多方
面。
8.跟踪机制（Tracing）
如果消息异常，RabbitMQ 提供了消息跟踪机制，使用者可以找出发生了什么。
9.插件机制（Plugin System）
RabbitMQ 提供了许多插件，来从多方面进行扩展，也可以编写自己的插件。 

## 1.3 架构图与主要概念 