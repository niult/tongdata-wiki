# 17_大数据NiFi--NiFi术语

[![Lansonli](https://pic1.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi术语**

## **一、DataFlow Manager**

DataFlow Manager(DFM)是NiFi用户，具有添加，删除和修改NiFi数据流组件的权限。

## **二、FlowFile**

FlowFile代表NiFi中的单个数据。FlowFile由属性(attribute)和内容(content)组成。内容是FlowFile表示的数据，属性由键值对组成，提供有关数据的信息或上下文的特征。所有FlowFiles都具有以下标准属性：

uuid：一个通用唯一标识符,用于区分各个FlowFiles。

filename：在将数据存储到磁盘或外部服务时可以使用的可读文件名

path：在将数据存储到磁盘或外部服务时可以使用的分层结构值,以便数据不存储在单个目录中。

## **三、Processor**

处理器是NiFi组件,用于监听传入数据、从外部来源提取数据、将数据发布到外部来源、路由,转换或从FlowFiles中提取信息。

## **四、Relationship**

每个处理器都有零个或多个关系。这些关系指示如何对FlowFile进行处理：处理器处理完FlowFile后,它会将FlowFile路由(传输)到其中一个关系。DFM能够将每一个关系连接到其他组件,以指定FlowFile应该在哪里进行下一步处理。

## **五、Connection**

Connection可以将不同的Processor连接在一起创建自动的数据处理流程。

## **六、Controller Service**

控制器服务是扩展点,在用户界面中由DFM添加和配置后,将在NiFi启动时启动,并提供给其他组件(如处理器或其他控制器服务)需要的信息。

## **七、Reporting Task**

报告任务在后台运行,以提供有关NiFi实例中发生情况的统计报告。

## **八、Funnel**

漏斗是一个NiFi组件,用于将来自多个Connections的数据合并到一个Connection中。

## **九、Process Group**

当数据流变得复杂时,在更高,更抽象的层面上管理数据流是很有用的。NiFi允许将多个组件(如处理器)组合到一个Process group 中。可以通过界面查看组和操作组中的组件。

## **十、Port**

一般用于远程连接NiFi组使用。

## **十一、Remote Process Group**

远程组可以实现将数据从一个NiFi实例传输到另一个NIFI实例。虽然NiFi提供了许多不同的机制来将数据从一个系统传输到另一个系统,但是如果将数据传输到另一个NiFi实例,远程进程组实现是最简单方法。

## **十二、Bulletin（公告）**

NiFi用户界面提供了大量有关应用程序当前状态的监视和反馈。除了每个组件"黄色三角形"的警告以外，每个组件运行有错误时还会报告错误公告，这个错误会显示在处理器的右上角，以红色图标显示。系统级公告显示在页面顶部附近的状态栏上。使用鼠标悬停在该图标上将提供一个工具提示,显示公告相关信息。

## **十三、Template**

DataFlow由许多可以重用的组件组成，NiFi允许DFM选择DataFlow的一部分(或整个DataFlow)并创建模板，达到复用的目的。

## **十四、flow.xml.gz**

用户界面画布的所有组件内容都实时写入一个名为flow.xml.gz的文件，该文件默认位于$NIFI_HOME/conf目录中。在画布上进行的任何更改都会自动保存到此文件中。此外,NiFi在更新时会自动备份此文件，您可以使用这些备份来回滚配置,如果想要回滚,先停止NiFi,将flow.xml.gz替换为所需的备份,然后重新启动NiFi。在集群环境中,停止整个NiFi集群,替换其中一个节点的flow.xml.gz,删除自其他节点的flow.xml.gz,然后重启集群，节点之间会自动同步"flow.xml.gz"备份文件。

发布于 2023-02-27 08:20・IP 属地广东