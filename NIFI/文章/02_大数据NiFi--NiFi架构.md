# （二）大数据NiFi--NiFi架构

[![Lansonli](https://pica.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=32738c0c)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



#### **一、NiFi核心概念**

NiFi的基本设计理念是基于数据流的编程Flow-Based Programming（FBP），应用是由处理器、连接器组成的网络。数据进入一个节点，由该节点对数据进行处理，根据不同的处理结果将数据路由到后续的其他节点进行处理。这是NiFi的流程比较容易可视化的一个原因。以下是NiFi的一些概念：

| NiFi术语           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| FlowFile           | FlowFile 是系统间传输的对象，FlowFile有attribute和content，attribute属性是与数据关联的key-value键值对，content内容是数据本身相关的字节流。 |
| FlowFile Processor | Processor 是实际操作数据的模块。Processor负责创建、接收、发送、转换、路由、拆分、合并、处理FlowFile。  Processor可以访问零到多个FlowFile的属性和内容，可以提交或回退提交的任务。 |
| Connection         | Connection用来连接Processor,每个Connection充当一个队列从而实现不同的Processor可以以不同的速率交互数据。这个队列可以动态调节优先级，也可以设置负载上限，实现反压机制。  Connection通常和Processor的一个或者多个Relationship连接，这就允许根据处理器的不同数据处理结果来路由数据。当一个FlowFile被发送到某个Relationship时，它就被加到了对应的COnnect队列里。 |
| Flow Controllers   | 负责维护Processors之间的调度、管理所有流程使用的线程及其分配。 |
| Process Group      | 处理器组，一堆Processors及其对应的Connection组成了一个Process Group,这个处理器组通过输入端口接收数据，通过输出端口发送数据。  Process Group可以组合其他的组件来创建新的组合。 |

参照上述表格，简单来讲FlowFile是在各个节点间流动的数据；FlowFile Processor 是数据的处理模块；Connection是各个处理模块间的一个队列；Flow Controllers是复杂流程的调度；Process Group一些相关连的Processor可以封装到一个Process Group中，不同组用来表示不同流程的层次关系。

这种设计模式带来了很多好处，帮助NiFi成为构建强大的可扩展数据流高效的平台，包括：

- 适用于可视化的创建和管理Processor。
- 本质上是异步的，即使在处理和流量波动时也允许非常高的吞吐和自然缓冲。
- 提供高并发的模型，让开发人员不用担心如何实现复杂的并发。
- 帮助高度聚合和松散耦合组件的开发，让这些组件可以在其他环境复用，并帮助单元测试。
- 资源受限的connection使得背压和压力释放等关键功能非常自然和直观。
- 错误处理做的非常好，而不是粗粒度的一把抓。
- 数据进入和退出系统以及如何流过的点很容易理解和轻松跟踪。

#### **二、NiFi架构**

![img](https://pic1.zhimg.com/80/v2-3b65d599dc0f656bbc84e3194294b008_1440w.webp)

NiFi是基于Java开发的，所以运行在JVM之上。NiFi的核心部件在JVM中的位置如上图：

- **Web Server (Web 服务器)：**

Web服务器的目的是承载NiFi基于http的命令和控制API。

- **Flow Controller(流控制器)：**

Flow Controller是NiFi执行具体操作的大脑，负责从线程资源池中给Processor分配可执行的线程，以及其他资源管理调度的工作。

- **Extensions(扩展):**

NiFi中有各种Processor及扩展。这些扩展也是运行在JVM中的。

- **FlowFile Repository(FlowFile 存储库):**

FlowFile Repository 负责保存在目前活动流中FlowFile的状态。FlowFile Repository的实现是可插拔的（多种选择，可配置，甚至可以自己实现），默认实现是使用Write-Ahead Log技术写到指定磁盘目录。

- **Content Repository(内容存储库):**

Content Repository负责保存在目前活动流中FlowFile的实际字节内容。其功能实现是可插拔的。默认的方式是一种相当简单的机制，即存储内容数据在文件系统中。多个存储路径可以被指定，因此可以将不同的物理路径进行结合，从而避免达到单个物理分区的存储上限。

- **Provenance Repository(源头数据库)：**

源存储库是存储所有源事件数据的地方，同样此功能是可插拔的，并且默认可以在一个或多个物理分区上进行存储，在每个路径下的事件数据都被索引，并且可被查询。

#### **三、NiFi集群架构**

![img](https://pic2.zhimg.com/80/v2-e244581bf534bbd70b4c8a748b467611_1440w.webp)

从NiFi 1.0版本开始，NiFi采用Zero-Master集群模式。NiFi集群中的每个节点都对数据执行相同的任务，但每个节点都运行在不同的数据集上。

- **zookeeper Client:**

NiFi依赖zookeeper进行协调各个节点，负责故障转移和选举NiFi节点。NiFi中依赖的zookeeper可以是NiFi自带的内置Zookeeper,也可以是用户安装的zookeeper集群。在搭建NiFi集群时，使用用户安装的zookeeper集群时zookeeper版本需要是3.5版本以上。

- **Cluster Coordinator-集群协调器:**

Apache ZooKeeper选择其中一个节点作为集群协调器，故障转移由ZooKeeper自动处理。所有集群节点都会向集群协调器报告心跳和状态信息。集群协调器负责断开和连接节点。

- **Primary Node-主节点：**

每个集群都有一个主节点，主节点也是由ZooKeeper选举产生。指定主节点是为了运行单节点任务，这种任务不适合在集群中运行的组件，例如：读取单节点文件，如果每个节点都读取数据文件会造成重复读取，这时可以配置主节点来指定从某个节点上执行。当主节点挂掉时，zookeeper也会重新选举主节点。

此外，我们可以通过集群中任何节点的UI与NiFi集群进行交互，所做的任何更改都会复制到集群中的所有节点。



发布于 2023-01-12 10:52・IP 属地广东



------

### **[基于流的编程（Flow-Based Programming）](https://www.cnblogs.com/yangwenhuan/p/12295409.html)**

基于流的编程（Flow-Based Programming，简称FBP），是一种数据流编程范式，有着一组独特的特性，同时是基于组件的软件工程方法的一种。FBP把一个应用看作一组进程（process），进程间通过连接（connection）进行通信，进程通过端口（port）来访问连接（这种抽象类似网络编程）。

1、进程：组件（component）的一个实例，可以跟其他进程并行运行。其他进程可以是同个组件的其他实例。

2、表示为一个有向图，其中进程作为节点，而连接作为边。

3、组件：对于应用开发者，通常可以看作黑盒；当要使用传统高级语言来创建组件或者组件本身是个子图时，它就是白盒。



------

### NIFI架构图

![img](https://pic3.zhimg.com/v2-bb0107833811e220d9cadeae120c326a_r.jpg)

