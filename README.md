# study-rocketmq

RocketMQ学习：从HelloWorld到最佳实践

## 1、[核心概念](http://rocketmq.apache.org/docs/core-concept/)

1. NameServer：一个简易的注册中心，保存Broker的路由信息。只要有两个功能：Broker管理和Routing管理。客户端从NameServer获取Broker路由信息，从而得知该与哪些Broker连接，发送和消费消息。
2. Broker：消息服务实体，负责消息接收、存储和处理来自消费者的拉取请求。它还存储与消息相关的元数据，包括使用者组，使用者进度偏移量和主题/队列信息。
3. Producer：消息生产者，将业务应用程序系统生成的消息发送给Broker。提供多种发送方式：同步，异步和单向。
4. Producer Group：生产者组，发送事务消息时，如果原始生产者发送Half Message后崩溃，则在后续步骤总，Broker可以连接同一生产者组的其他生产者实例以确认提交或回滚交易。对非事务消息没什么实质作用。注意，考虑到生产者在发送消息方面足够强大，因此每个JVM中每个生产者组建议只使用一个实例，以避免不必要的生产者实例初始化（可以通过设置Producer的线程池数量来控制吞吐量）。
5. Consumer：消息消费者，有两种方式从Broker消费消息：推和拉。
    - Pull Consumer：应用主动调用Consumer的拉消息方法从Broker拉消息，主动权由应用控制。
    - Push Consumer：应用向Consumer对象注册一个Listener接口，一旦收到消息，Consumer对象立刻回调Listener接口方法。
6. Consumer Group：消费者组，消费者组是一个很棒的概念，通过它，在消息消耗方面实现负载平衡和容错的目标非常容易。注意，消费者者组的消费者实例必须具有完全相同的主题（Topic+Tag）订阅。
7. Topic：消息主题，第一级消息类型。
8. Tag：消息标签，第二级消息类型，可以基于Tag做消息过滤。  
   `Topic：order`  
   `Tag：order-new，order-pay，order-complete`
9. Message：消息。
10. Message Queue：消息队列，顾名思义，存消息的。一个Topic对应多个Message Queue，可以设置。
11. Message Model：消息模式（称其消息消费模式更为确切），包括集群消息和广播消息。
    - 集群消息，一个Consumer Group中的Consumer实例平均分摊消费消息。例如，某个Topic有9条消息，其中一个Consumer Group有3个实例（可能是3个迕程，或者3台机器），那么每个实例只消费其中的3条消息。
    - 广播消息，一条消息被多个Consumer消费，即使这些Consumer属于同一个Consumer Group，消息也会被Consumer Group中的每个Consumer都消费一次。
12. Message Order：消息顺序，包括顺序消息和无序消息。
    - 顺序消息，相同Topic的消息发送到相同的Message Queue；（思考一个问题：消息消费怎么保证顺序呢）
    - 无序消息，相同Topic的消息负载均衡地发送的不同的Message Queue；

## 2、各种消息

1. 顺序消息
2. 定时消息
3. 事务消息

> SendResult [sendStatus=SEND_OK, msgId=0A42333A0DC818B4AAC246C290FD0000, offsetMsgId=0A42333A00002A9F000000000134F1F5, messageQueue=MessageQueue [topic=topicTest1, BrokerName=mac.local, queueId=3], queueOffset=4]
>- **msgId**：对于客户端来说msgId是由客户端producer实例端生成的，
   具体来说，调用方法MessageClientIDSetter.createUniqIDBuffer()生成唯一的Id；  
>- **offsetMsgId**：offsetMsgId是由Broker服务端在写入消息时生成的（采用”IP地址+Port端口”与“CommitLog的物理偏移量地址”做了一个字符串拼接），
   其中offsetMsgId就是在RocketMQ控制台直接输入查询的那个messageId；

## 3、最佳实践

1. Producer([官网](http://rocketmq.apache.org/docs/best-practice-producer/)|[万网](http://rocketmq.cloud/zh-cn/docs/bestpractice-producer.html))
2. Consumer([官网](http://rocketmq.apache.org/docs/best-practice-consumer/)|[万网](http://rocketmq.cloud/zh-cn/docs/bestpractice-consumer.html))
3. Broker([官网](http://rocketmq.apache.org/docs/best-practice-broker/)|[万网](http://rocketmq.cloud/zh-cn/docs/bestpractice-broker.html))
4. NameServer([官网](http://rocketmq.apache.org/docs/best-practice-namesvr/)|[万网](http://rocketmq.cloud/zh-cn/docs/bestpractice-namesrv.html))
5. JVM/Kernel([官网](http://rocketmq.apache.org/docs/system-config/)|[万网](http://rocketmq.cloud/zh-cn/docs/configuration-system.html))

## 4、功能扩展  

