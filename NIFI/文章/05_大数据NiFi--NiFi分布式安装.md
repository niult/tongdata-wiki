# （五）大数据NiFi--NiFi分布式安装

[![Lansonli](https://pic1.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi分布式安装**

## **一、为什么需要NiFi集群**

NiFi DataFlow Manager(DFM)用户可能会发现在单个服务器上使用一个NiFi实例不足以处理他们拥有的数据量。因此，一种解决方案是在多个NiFi服务器上运行相同的数据流。但是，这会产生管理问题，因为每次DFM想要更改或更新数据流时，他们必须在每个服务器上进行这些更改，然后单独监视每个服务器。通过集群NiFi服务器，可以增加处理能力以及单个接口，通过该接口可以更改数据流并监控数据流。集群允许DFM仅进行一次更改，然后将更改复制到集群的所有节点。通过单一接口，DFM还可以监视所有节点的健康状况和状态。

![img](https://pic4.zhimg.com/80/v2-ae7d85bcabfea34da4035154a48ad68b_1440w.webp)



在前文中我们已经介绍了NiFi集群中的角色，Cluster Coordinator 负责执行任务、管理集群中的节点，并且为新加入的节点提供数据。

NiFi集群是由一个或者多个节点组成，节点进行数据处理，节点通过心跳向集群协调器上报健康情况和状态，默认情况下，节点每5秒发出一次心跳，如果集群协调器在5秒内没有从节点上接收到心跳，则会断开节点。

每个集群都有一个主节点，主节点上可以运行“独立处理器”，Zookeeper用于自动选择主节点，用户可以通过WebUI界面查看当前集群中的主节点。以上在主节点上运行的“独立处理器”指的是在NiFi集群中，处理数据流的处理器在每个节点上运行，我们不希望相同的数据流在每个节点上都被处理器处理，例如：GetSFTP处理器从远程目录中提取数据，如果GetSFTP处理器在集群中的每个节点上运行并同时从同一个远程目录中提取数据，则数据会被重复处理，因此我们可以将GetSFTP处理器设置为“独立处理器”，这意味着该处理器只会在主节点上运行。通过适当的数据流配置，主节点可以将数据接收来之后在集群其他节点中进行负载均衡。

NiFi分布式安装可以使用内嵌的zookeeper，也可以使用自己安装好的zookeeper集群，这种配置相对简单，经测试使用外部zookeeper安装NiFi集群，NiFi1.13.0对应的zookeeper版本至少是3.5以上。由于NiFi不同版本使用的zookeeper版本不同，建议使用内嵌的zookeeper完成NiFi集群搭建。

## **二、使用内嵌的zookeeper搭建NiFi集群**

使用内嵌zookeeper搭建NiFi集群，zookeeper节点配置也需要奇数台，这里使用三台节点配置内嵌zookeeper，同时搭建NiFi集群节点也是三台，搭建步骤如下：

### **1、划分节点，上传解压NiFi安装包**

安装NiFi集群可以使用多个节点，这里安装NiFi集群选择三台节点:node1、node2、node3。每台节点上需要安装好JDK8。

```text
#上传解压安装包
[root@node1 software]# tar -zxvf ./nifi-1.13.0-bin.tar.gz

#将安装包发送到node2、node3节点上
[root@node1 software]# scp -r ./nifi-1.13.0 node2:/software/
[root@node1 software]# scp -r ./nifi-1.13.0 node3:/software/
```



### **2、配置使用内嵌zookeeper**

在node1、node2、node3每台节编辑 $NIFI_HOME/conf/zookeeper.properties文件,后面追加以下内容（注意删除这个文件的最后一行再追加），配置zookeeper：

```text
#配置zookeeper的端口号，为了避免端口冲突，可以配置2182
clientPort=2182
#配置不同服务的ip
server.1=node1:2888:3888
server.2=node2:2888:3888
server.3=node3:2888:3888
```



在node1、node2、node3每台节点新建文件夹 $NIFI_HOME/state/zookeeper,在此文件夹下创建文件myid，分别写入1,2,3

```text
#在node1、node2、node3节点创建zookeeper路径
cd /software/nifi-1.13.0
mkdir -p ./state/zookeeper

#在node1 $NIFI_HOME/state/zookeeper中创建myid文件写入1
#在node2 $NIFI_HOME/state/zookeeper中创建myid文件写入2
#在node3 $NIFI_HOME/state/zookeeper中创建myid文件写入3
```



### **3、每台节点编辑 $NIFI_HOME/conf/nifi.properties文件**

以下以node1配置为例：

```text
#指定NiFi是否使用内嵌的zookeeper，默认false
nifi.state.management.embedded.zookeeper.start=true

#配置Http主机节点，每台节点配置的host不同
nifi.web.http.host=node1
#配置Http端口，默认是8080
nifi.web.http.port=8989

#如果实例是集群中的节点，请将此设置为true。默认值为false
nifi.cluster.is.node=true
#节点的完全限定地址。默认为空白，每台节点配置不同
nifi.cluster.node.address=node1
#节点的协议端口。默认为空白
nifi.cluster.node.protocol.port=28001
#指定集群中所需的节点数，以便提前选择流。这允许集群中的节点避免在开始处理之前等待很长时间
nifi.cluster.flow.election.max.candidates=1

#连接内嵌ZooKeeper连接地址
nifi.zookeeper.connect.string=node1:2182,node2:2182,node3:2182
```



注意：在每台节点配置以上nifi.properties文件时，以下配置项每台节点不同：

```text
#node2/3节点需要配置nifi.properties文件，修改对应的IP
nifi.web.http.host=node2/node3
nifi.cluster.node.address=node2/node3
```



### **4、node1、node2、node3每台节点配置 $NIFI_HOME/conf/state-management.xml文件**

```text
<cluster-provider>
    <id>zk-provider</id>
    <class>org.apache.nifi.controller.state.providers.zookeeper.ZooKeeperStateProvider</class>
    <property name="Connect String">node1:2182,node2:2182,node3:2182</property>
    <property name="Root Node">/nifi</property>
    <property name="Session Timeout">10 seconds</property>
    <property name="Access Control">Open</property>
</cluster-provider>
```



### **5、在node1、node2、node3节点分别注册nifi系统服务，并分别启动nifi**

```text
#在node1、node2、node3节点分别注册nifi服务
cd /software/nifi-1.13.0/bin
./nifi.sh install
Service nifi installed

#在node1、node2、node3节点分别启动集群
service nifi start
```



### **6、访问webui**

启动之后需要等待一段时间访问WebUI。通过node1，node2，node3三台节点任意节点都可以访问NiFi集群，在浏览器中输入**[http://node1:8989/nifi/](https://link.zhihu.com/?target=http%3A//node1%3A8989/nifi/)[1]** 访问NiFi集群。查看NiFi集群主节点：

![img](https://pic1.zhimg.com/80/v2-bd9a1a9eb2414aadc4b3a2011271af94_1440w.webp)

## **三、使用外部zookeeper搭建NiFi集群**

使用外部zookeeper搭建NiFi集群，NiFi1.13.0需要的zookeeper版本为3.5以上。搭建步骤如下：

### **1、划分节点，准备基础环境。**

安装NiFi集群可以使用多个节点，这里安装NiFi集群选择三台节点:node1、node2、node3。每台节点上需要安装好JDK8。

### **2、在node1上传解压NiFi安装包,编辑 $NIFI_HOME/conf/nifi.properties文件**

```text
#配置Http主机节点
nifi.web.http.host=192.168.179.4

#配置Http端口，默认是8080
nifi.web.http.port=8989

#如果实例是集群中的节点，请将此设置为true。默认值为false
nifi.cluster.is.node=true
#节点的完全限定地址。默认为空白
nifi.cluster.node.address=192.168.179.4
#节点的协议端口。默认为空白
nifi.cluster.node.protocol.port=28001
#指定集群中所需的节点数，以便提前选择流。这允许集群中的节点避免在开始处理之前等待很长时间
nifi.cluster.flow.election.max.candidates=1

#连接外部ZooKeeper连接地址
nifi.zookeeper.connect.string=node3:2181,node4:2181,node5:2181
```



### **3、在node1节点上配置 $NIFI_HOME/conf/state-management.xml配置外部zookeeper集群**

```text
<cluster-provider>
    <id>zk-provider</id>
    <class>org.apache.nifi.controller.state.providers.zookeeper.ZooKeeperStateProvider</class>
    <property name="Connect String">node3:2181,node4:2181,node5:2181</property>
    <property name="Root Node">/nifi</property>
    <property name="Session Timeout">10 seconds</property>
    <property name="Access Control">Open</property>
</cluster-provider>
```



### **4、将node1配置好的NiFi发送到node2、node3并在node2/3上配置**

```text
[root@node1 software]# scp -r ./nifi-1.13.0 node2:/software/
[root@node1 software]# scp -r ./nifi-1.13.0 node3:/software/

#node2/3节点需要配置nifi.properties文件，修改对应的IP
nifi.web.http.host=192.168.179.5/6
nifi.cluster.node.address=192.168.179.5/6
```



### **5、启动zookeeper**

### **6、在node1、node2、node3节点分别注册nifi系统服务，并分别启动nifi**

```text
#在node1、node2、node3节点分别注册nifi服务
cd /software/nifi-1.13.0/bin
./nifi.sh install
Service nifi installed

#在node1、node2、node3节点分别启动集群
service nifi start
```



### **7、访问WebUI，查看集群Master节点**

启动之后需要等待一段时间访问WebUI。通过node1，node2，node3三台节点任意节点都可以访问NiFi集群，在浏览器中输入**[http://node1:8989/nifi/](https://link.zhihu.com/?target=http%3A//node1%3A8989/nifi/)[2]** 访问NiFi集群。查看NiFi集群主节点：

![img](https://pic1.zhimg.com/80/v2-bd9a1a9eb2414aadc4b3a2011271af94_1440w.webp)

### **参考资料**

[1] [http://node1:8989/nifi/:](https://link.zhihu.com/?target=http%3A//node1%3A8989/nifi/%3A) *[http://node1:8989/nifi/](https://link.zhihu.com/?target=http%3A//node1%3A8989/nifi/)*

[2] [http://node1:8989/nifi/:](https://link.zhihu.com/?target=http%3A//node1%3A8989/nifi/%3A) *[http://node1:8989/nifi/](https://link.zhihu.com/?target=http%3A//node1%3A8989/nifi/)*

发布于 2023-01-25 09:23・IP 属地广东