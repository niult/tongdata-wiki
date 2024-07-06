# 16_大数据NiFi--处理器Connection连接

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **处理器Connection连接**

## **一、查看队列中的FlowFile**

单独启动“GenerateFlowFile”处理器后，可以观察到对应的Connection连接队列中有数据，在Connection连接上右键“List Queue”可以查看队列中的FlowFile信息：

![img](https://pic1.zhimg.com/80/v2-2bb87800fc0369f6dd4891ba61f12fbc_1440w.webp)



![img](https://pic1.zhimg.com/80/v2-170a493e601c8a12b4e5df8843fd1ea0_1440w.webp)



## **二、查看FlowFile自定义属性值**

队列中的FlowFile属性中还可以查看自定义的属性信息，例如：在“GenerateFlowFile”处理器中设置自定义属性“mykey”，对应的value值设置为“myvalue”：

![img](https://pic2.zhimg.com/80/v2-7acc9292dd19aae73b2c465bccdb8b61_1440w.webp)

单独启动“GenerateFlowFile”生产部分数据，查看队列中的FlowFile属性如下：

![img](https://pic1.zhimg.com/80/v2-2bb5bab37cbaae93f0e13698f6c7f45c_1440w.webp)

## **三、Connection 配置**

针对Connectiond连接，可以通过“Configure”配置更多信息：

![img](https://pic1.zhimg.com/80/v2-8c972a45ec69ecabe1cd195c7f2fd13c_1440w.webp)

弹出页面点击“SETTINGS”：

![img](https://pic4.zhimg.com/80/v2-de80e78760d08f8e120b2cf8333fc73f_1440w.webp)

**“FlowFile expiration”数据过期：**

设置FlowFile expiration 时间可以删除队列中无法及时处理的数据，默认设置为0，数据永远不会过期，当设置了一个过期时间，在Connect连接上可以看到一个小时钟图标。

![img](https://pic4.zhimg.com/80/v2-d8acc2745ab461a251f11d489ddb43c3_1440w.webp)

**“Back Press”背压：**

NiFi提供了两种背压配置机制，背压机制允许在队列中存在多少数据，当达到这个数据后，源头处理器就不再调度产生数据，防止数据溢出。"Back pressure object threshold"指定队列中的数据达到多少条数触发背压机制。"Back pressure data size threshold"指定队列中数据达到多大时触发背压机制。设置背压机制后，在Connection连接上会显示进度条。

![img](https://pic3.zhimg.com/80/v2-c6a0950fb6ab92c62e7a0fe1b5521c66_1440w.webp)

**“load balance strategies”负载均衡：**

负载均衡策略可选项如下：

- Do not load balance：不在集群中均衡数据，默认值。
- Partition by attribute：根据用户指定的属性值决定数据发往哪个节点，相同属性值的数据会发往同一个节点。如果数据发往的节点断开连接，数据不会发送到其他节点，当节点再次连接到集群时，会自动发送。
- Round robin：轮询方式分发数据，当一个节点断开时，会自动轮询发送到其他节点。
- Single node：所有数据发送到单节点，不可配置哪个节点。当节点与集群断开后，数据不会发送到其他节点，直到该节点再次可用。

设置好负载均衡后可以看到图标：

![img](https://pic1.zhimg.com/80/v2-18710c0d62b77b255d61d9154302b60c_1440w.webp)

**“Load Balance Compression”负载均衡数据压缩：**

当配置好负载均衡策略后，可以设置节点间数据传递是否压缩，可选项如下：

- Do not compress:不压缩。
- Compress attributes only：只压缩属性。
- Compress attributes and content: 压缩属性和内容。

**"Select Prioritization"优先级：**

可以指定如何对队列中的数据进行优先级排序以便处理优先级高的数据。可以从"Available prioritizers" 拖动到"Selected prioritizers"标签下使用优先级。可以选择多个优先级，数据会依次按照选择的优先级排序，优先级可选如下：

- FirstInFirstOutPrioritizer：给定两个FlowFiles,首先处理首先到达连接的FlowFiles。
- NewestFlowFileFirstPrioritizer：给定两个FlowFiles,将首先处理数据流中最新的FlowFiles。
- OldestFlowFileFirstPrioritizer：给定两个FlowFiles,将首先处理数据流中最旧的FlowFiles。**这是在没有选择优先级的情况下使用的默认方案。**
- PriorityAttributePrioritizer：给定两个FlowFiles,将提取名为priority的属性。将首先处理具有最低优先级值的那个。建议设置数值，如果是字符串会按照ascii码排序。

## **四、查看Connections信息**

在主菜单“Summary”中的“Connection”中可以查看连接的信息：

![img](https://pic1.zhimg.com/80/v2-2c8f844f7ad5210503edab589a9249d8_1440w.webp)

## **五、设置弯曲连接**

可以双击Connection连接线来增加弯曲点，对弯曲点再次双击将删除当前弯曲点。

![img](https://pic4.zhimg.com/80/v2-1b9e593f3861bc6edbc9ca3435b60597_1440w.webp)

发布于 2023-02-26 20:03・IP 属地广东