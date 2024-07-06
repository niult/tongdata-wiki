# （六）大数据NiFi--NiFi Processors（处理器）

[![Lansonli](https://pic1.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi Processors（处理器）**

为了创建高效的数据流处理流程,需要了解可用的处理器（Processors ）类型，NiFi提供了大约近300个现成的处理器。这些处理器提供了可从不同系统中提取数据,路由,转换,处理,拆分和聚合数据以及将数据分发到多个系统的功能。如果还不能满足需求，还可以自定义处理器。

每个新的NiFi版本都会有新的处理器，下面将按照功能对处理器分类，介绍一些常用的处理器。具体可参照官网查看更多的处理器信息：

**[http://nifi.apache.org/docs/nifi-docs/html/getting-started.html#what-processors-are-available](https://link.zhihu.com/?target=http%3A//nifi.apache.org/docs/nifi-docs/html/getting-started.html%23what-processors-are-available)**

## **一、数据提取**

- GetFile：将文件内容从本地磁盘（或网络连接的磁盘）流式传输到NiFi,然后删除原始文件。此处理器应将文件从一个位置移动到另一个位置,而不是用于复制数据。
- GetHDFS：监视HDFS中用户指定的目录。每当新文件进入HDFS时,它将被复制到NiFi并从HDFS中删除。此处理器应将文件从一个位置移动到另一个位置,而不是用于复制数据。如果在集群中运行,此处理器需仅在主节点上运行。
- GetKafka：从Apache Kafka获取消息,封装为一个或者多个FlowFile。

## **二、数据转换**

- ReplaceText：使用正则表达式修改文本内容。
- SplitText：SplitText接收单个FlowFile,其内容为文本,并根据配置的行数将其拆分为1个或多个FlowFiles。例如,可以配置处理器将FlowFile拆分为多个FlowFile,每个FlowFile只有一行。
- SplitJson：将JSON对象拆分成多个FlowFile。

## **三、数据出口/发送数据**

- PutFile：将FlowFile的内容写入指定的目录。
- PutSQL：将FlowFile的内容作为SQL语句（INSERT,UPDATE或DELETE）执行,该处理器将执行sql语句,同时支持参数化的SQL语句。
- PutKafka：将FlowFile的内容作为消息发送到Apache Kafka,可以将FlowFile中整个内容作为一个消息也可以指定分隔符将其封装为多个消息发送。
- PutHDFS : 将FlowFile数据写入Hadoop分布式文件系统HDFS。

## **四、数据库访问**

- ExecuteSQL：执行用户定义的SQL SELECT命令，将结果写入Avro格式的FlowFile。
- PutSQL：通过执行SQL DDM语句来更新数据库。
- ConvertJSONToSQL：将JSON文档转换为SQL INSERT或UPDATE命令,然后可以将其传递给PutSQL Processor。
- SelectHiveQL：对Apache Hive执行HQL SELECT命令，将结果写入Avro或CSV格式的FlowFile。
- PutHiveQL：通过执行FlowFile内容定义的HiveQL DDM语句来更新Hive数据库。
- QueryDatabaseTable : 数据库查询处理器，支持: mysql，查询结果将被转换为Avro格式，与ExecuteSQL功能一样。

## **五、提取属性**

- EvaluateJsonPath：用户提供JSONPath表达式，这个表达式将对Json内容操作，将表达式计算的结果值替换FlowFile内容或将结果值提取到用户自己命名的Attribute中。
- ExtractText：用户提供一个或多个正则表达式,然后根据FlowFile的文本内容对其进行评估,然后将结果值提取到用户自己命名的Attribute中。

发布于 2023-01-26 10:13・IP 属地广东