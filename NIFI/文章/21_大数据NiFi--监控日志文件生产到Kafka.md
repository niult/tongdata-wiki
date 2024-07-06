# 21_大数据NiFi--监控日志文件生产到Kafka

[![Lansonli](https://pic1.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **监控日志文件生产到Kafka**

案例：监控某个目录下的文件内容，将消息生产到Kafka中。

此案例使用到“TailFile”和“PublishKafka_1_0”处理器。

## **一、配置“TailFile”处理器**

创建“TailFile”处理器并配置：

![img](https://pic1.zhimg.com/80/v2-d2cbc9e2175c991448e21f8c74791774_1440w.webp)

注意：以上需要在NiFi集群中的每个节点上创建“/root/test/logdata”文件，“logdata”是文件，而非目录。

## **二、配置“PublishKafka_1_0”处理器**

“PublishKafka_1_0”处理器作用是使用Kafka 1.0生产者API将FlowFile的内容作为消息发送给Apache Kafka。发送的内容可以是单独的FlowFile，也可以通过用户指定分隔符分割的FlowFile内容。

关于“PublishKafka_1_0”处理器的“Properties”主要配置的说明如下：

| 配置项                               | 默认值         | 允许值        | 描述                                                         |
| ------------------------------------ | -------------- | ------------- | ------------------------------------------------------------ |
| Kafka Brokers  （Kafka节点）         | localhost:9092 |               | 逗号分割的Kafka集群Broker列表。格式：host:port               |
| Topic Name  （topic 名称）           |                |               | 将消息生产到的Topic 名称。                                   |
| Delivery Guarantee  （数据传递保证） | 0              |               | 指定保证消息被发送到Kafka的要求。对应Kafka的'acks'属性。  可以配置的项如下：   Best Effort （尽力交付，相当于ack=0）:   在向Kafka节点写出消息后，FlowFile将被路由到成功，而不需要等待响应。这提供了最好的性能，但可能会导致数据丢失。例如：消息写出到Kafka节点，但是对应节点挂掉，这时将消息路由到成功。   Guarantee Single Node Delivery(保证单节点交付，相当于ack=1，Kafka中的默认配置):   KafkaProducer把消息发送出去，至少要等待leader已经成功将数据写入本地log，但是并没有等待所有follower是否成功写入。该情况下，如果follower没有成功备份数据，而此时leader刚好又挂掉了，就会导致消息丢失。该选项就是如果消息被单个Kafka节点接收到，FlowFile将被路由到成功，无论它是否被复制，但如果Kafka节点崩溃，可能会导致数据丢失。  Guarantee Replicated Delivery(保证复制交付，相当于ack=-1):  FlowFile数据写出后，Kafka topic ISR列表离跟leader保持同步的那些follower都要把消息同步过去,该消息才会被认为成功，否则路由到失败。 |
| Use Transactions  （使用事务）       | true           | ▪true  ▪false | 指定NiFi是否应该在与Kafka通信时提供事务性保证。如果发送数据到Kafka有问题，并且这个属性设置为false，那么已经发送到Kafka的消息将继续发送，并被传递给消费者。如果这个设置为true，那么Kafka事务将被回滚，这样这些消息对消费者是不可用的。将此设置为true需要将属性设置为"Guarantee Replicated Delivery"。 |

- Best Effort （尽力交付，相当于ack=0）:

在向Kafka节点写出消息后，FlowFile将被路由到成功，而不需要等待响应。这提供了最好的性能，但可能会导致数据丢失。例如：消息写出到Kafka节点，但是对应节点挂掉，这时将消息路由到成功。

- Guarantee Single Node Delivery(保证单节点交付，相当于ack=1，Kafka中的默认配置):

KafkaProducer把消息发送出去，至少要等待leader已经成功将数据写入本地log，但是并没有等待所有follower是否成功写入。该情况下，如果follower没有成功备份数据，而此时leader刚好又挂掉了，就会导致消息丢失。该选项就是如果消息被单个Kafka节点接收到，FlowFile将被路由到成功，无论它是否被复制，但如果Kafka节点崩溃，可能会导致数据丢失。 Guarantee Replicated Delivery(保证复制交付，相当于ack=-1): FlowFile数据写出后，Kafka topic ISR列表离跟leader保持同步的那些follower都要把消息同步过去,该消息才会被认为成功，否则路由到失败。 **Use Transactions** **（使用事务）** true true false 指定NiFi是否应该在与Kafka通信时提供事务性保证。如果发送数据到Kafka有问题，并且这个属性设置为false，那么已经发送到Kafka的消息将继续发送，并被传递给消费者。如果这个设置为true，那么Kafka事务将被回滚，这样这些消息对消费者是不可用的。将此设置为true需要将属性设置为"Guarantee Replicated Delivery"。

“PublishKafka_1_0”处理器配置如下：

### **1、创建“PublishKafka_1_0”处理器**

![img](https://pic2.zhimg.com/80/v2-7e202d0de4cf9eec31132305b2d8e881_1440w.webp)

### **2、配置“PROPERTIES”**

![img](https://pic1.zhimg.com/80/v2-9ba20bc325cb9a7cd5abfebffce2a430_1440w.webp)

注意：以上topic 可以在Kafka中创建好，也可以执行时自动创建。

### **3、连接“TailFile”处理器和“PublishKafka_1_0”处理器**

连接“TailFile”处理器和“PublishKafka_1_0”处理器，并设置“PublishKafka_1_0”处理器“failure”和“success”路由关系为自动终止。

![img](https://pic1.zhimg.com/80/v2-d62914376ae75726fa9057707071728c_1440w.webp)

![img](https://pic2.zhimg.com/80/v2-3b9ed1d55c627b8f53c992a6cf88b9fd_1440w.webp)



## **三、运行测试**

### **1、启动Kafka集群，启动NiFi处理流程**

### **2、向/root/test/logdata文件中写入数据并保存**

向NiFi集群中的其中一台节点的“logdata”中写入以下数据即可

```text
[root@node1 test]# echo "hello world1" > /root/test/logdata
[root@node1 test]# echo "hello world2" >> /root/test/logdata
[root@node1 test]# echo "hello world3" >> /root/test/logdata
```



### **3、查看Kafka中自动创建的“nifi_topic”中的数据**

![img](https://pic1.zhimg.com/80/v2-b26ea446c28a797ae83fa6653fc7e9d0_1440w.webp)

以上数据每写入一行，有个空行，这是由于“TailFile”处理器监控数据导致的，实际就是写入了3条数据，可以通过后期业务处理时，对数据进行trim处理即可。

发布于 2023-06-17 09:31・IP 属地广东