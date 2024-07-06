# 18_大数据NiFi--离线同步MySQL数据到HDFS

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **离线同步MySQL数据到HDFS**



案例：使用NiFi将MySQL中数据导入到HDFS中。

以上案例用到的处理器有“QueryDatabaseTable”、“ConvertAvroToJSON”、“SplitJson”、“PutHDFS”四个处理器。

## **一、配置“QueryDatabaseTable”处理器**

该处理器主要使用提供的SQL语句或者生成SQL语句来查询MySQL中的数据，查询结果转换成Avro格式。该处理器只能运行在主节点上。

关于“QueryDatabaseTable”处理器的“Properties”配置的说明如下：

| 配置项                                                    | 默认值    | 允许值      | 描述                                                         |
| --------------------------------------------------------- | --------- | ----------- | ------------------------------------------------------------ |
| Database Connection Pooling Service  （数据库连接池服务） |           |             | 用于获得与数据库的连接的Controller Service。                 |
| Database Type  （数据库类型）                             | Generic   |             | 选择数据库类型。   Generic 通用类型 Oracle Oracle 12+ MS SQL 2012+ MS SQL 2008 MySQL PostgreSQL |
| Table Name  （表名）                                      |           |             | 查询数据库的表名，当使用“Custom Query”时，此为查询结果的别名，并作为FlowFile中的属性。 |
| Columns to Return  （返回的列）                           |           |             | 查询返回的列，多个列使用逗号分隔。如果列中有特殊名称需要加引号，则所有列都需要加引号处理。 |
| Additional WHERE clause  （where条件）                    |           |             | 在构建SQL查询时添加到WHERE条件中的自定义子句。               |
| Custom Query  （自定义SQL查询）                           |           |             | 自定义的SQL语句。该查询被构建成子查询，设置后不会从其他属性构建SQL查询。自定义SQL不支持Order by查询。 |
| Maximum-value Columns  （最大值列）                       |           |             | 指定增量查询获取最大值的列，多列使用逗号分开。指定后，这个处理器只能检索到添加/更新的行。不能设置无法比较大小的列，例如：boolean/bit。如果不指定，则参照表中所有的列来查询全量数据，这会对性能产生影响。 |
| Max Wait Time  （最大超时时间）                           | 0 seconds |             | SQL查询最大时长，默认为0没有限制，设置小于0的时间默认为0。   |
| Fetch Size  （拉取数据量）                                | 0         |             | 每次从查询结果中拉取的数据量。                               |
| Max Rows Per Flow File  （每个FlowFile行数）              | 0         |             | 在一个FlowFile文件中的数据行数。通过这个参数可以将很大的结果集分到多个FlowFile中。默认设置为0，所有结果存入一个FlowFile。 |
| Output Batch Size  （数据输出批次量）                     | 0         |             | 输出的FlowFile批次数据大小，当设置为0代表所有数据输出到下游关系。如果数据量很大，则有可能下游很久没有收到数据，如果设置了，则每次达到该数据量就释放数据，传输到下游。 |
| Maximum Number of Fragments  （最大片段数）               | 0         |             | 设置返回的最大数据片段数，设置0默认将所有数据片段返回，如果表非常大，设置后可以防止OOM错误。 |
| Normalize Table/Column Names  （标准表/列名）             | false     | true  false | 是否将列名中不兼容avro的字符修改为兼容avro的字符。例如，冒号和句点将被更改为下划线，以构建有效的Avro记录。 |
| Transaction Isolation Level                               |           |             | 设置事务隔离级别。                                           |
| Use Avro Logical Types  (使用Avro逻辑类型)                | false     | true  false | 是否对DECIMAL/NUMBER, DATE, TIME 和 TIMESTAMP 列使用Avro逻辑类型。 |
| Default Decimal Precision  （Decimal数据类型位数）        | 10        |             | 当 DECIMAL/NUMBER 数据类型转换成Avro类型数据时，指定的数据位数。 |
| Default Decimal Scale  （Decimal 数据类型小数位数）       | 0         |             | 当 DECIMAL/NUMBER 数据类型转换成Avro类型数据时，指定的小数点后的位数。 |

- Generic 通用类型
- Oracle
- Oracle 12+
- MS SQL 2012+
- MS SQL 2008
- MySQL
- PostgreSQL
  **Table Name****（表名）**
  查询数据库的表名，当使用“Custom Query”时，此为查询结果的别名，并作为FlowFile中的属性。 Columns to Return （返回的列）
  查询返回的列，多个列使用逗号分隔。如果列中有特殊名称需要加引号，则所有列都需要加引号处理。 Additional WHERE clause （where条件）
  在构建SQL查询时添加到WHERE条件中的自定义子句。 Custom Query （自定义SQL查询）
  自定义的SQL语句。该查询被构建成子查询，设置后不会从其他属性构建SQL查询。自定义SQL不支持Order by查询。 Maximum-value Columns （最大值列）
  指定增量查询获取最大值的列，多列使用逗号分开。指定后，这个处理器只能检索到添加/更新的行。不能设置无法比较大小的列，例如：boolean/bit。如果不指定，则参照表中所有的列来查询全量数据，这会对性能产生影响。 **Max Wait Time****（最大超时时间）** 0 seconds
  SQL查询最大时长，默认为0没有限制，设置小于0的时间默认为0。 **Fetch Size****（拉取数据量）** 0
  每次从查询结果中拉取的数据量。 **Max Rows Per Flow File****（每个FlowFile行数）** 0
  在一个FlowFile文件中的数据行数。通过这个参数可以将很大的结果集分到多个FlowFile中。默认设置为0，所有结果存入一个FlowFile。 **Output Batch Size****（数据输出批次量）** 0
  输出的FlowFile批次数据大小，当设置为0代表所有数据输出到下游关系。如果数据量很大，则有可能下游很久没有收到数据，如果设置了，则每次达到该数据量就释放数据，传输到下游。 **Maximum Number of Fragments****（最大片段数）** 0
  设置返回的最大数据片段数，设置0默认将所有数据片段返回，如果表非常大，设置后可以防止OOM错误。 **Normalize Table/Column Names****（标准表/列名）** false true false 是否将列名中不兼容avro的字符修改为兼容avro的字符。例如，冒号和句点将被更改为下划线，以构建有效的Avro记录。 Transaction Isolation Level
  设置事务隔离级别。 **Use Avro Logical Types****(使用Avro逻辑类型)** false true false 是否对DECIMAL/NUMBER, DATE, TIME 和 TIMESTAMP 列使用Avro逻辑类型。 **Default Decimal Precision****（Decimal数据类型位数）** 10
  当 DECIMAL/NUMBER 数据类型转换成Avro类型数据时，指定的数据位数。 **Default Decimal Scale****（Decimal 数据类型小数位数）** 0
  当 DECIMAL/NUMBER 数据类型转换成Avro类型数据时，指定的小数点后的位数。

配置步骤如下：

**1、新建“QueryDatabaseTable”处理器**

![img](https://pic1.zhimg.com/80/v2-9d19cb569da5bba6f4ce104075129a1c_1440w.webp)

请在此添加图片描述

**2、配置“SCHEDULING”调度时间**

这里调度时间配置为99999s,读取数据库，这里读取一次即可，默认0会不间断读取数据库会对服务器造成非常大压力。执行仅支持“Primary”主节点运行。

![img](https://pic2.zhimg.com/80/v2-100036863ac99b4b4f58e834c335a22d_1440w.webp)

请在此添加图片描述

**3、配置“PROPERTIES”**

配置“Database Connection Pooling Service”选择创建，在弹出页面中可以按照默认选择直接点击“Create”。

![img](https://pic3.zhimg.com/80/v2-53f6ee1ae51de08b662ae5460745250e_1440w.webp)

请在此添加图片描述

点击“->”继续配置MySQL连接：

![img](https://pic1.zhimg.com/80/v2-52b955245ec149c0be57ff991dbd7844_1440w.webp)

请在此添加图片描述

![img](https://pic2.zhimg.com/80/v2-2c713611f8c4878076f74c94a6a7d069_1440w.webp)

请在此添加图片描述

在弹出的页面中填入：

- 连接MysqlURL:

jdbc:mysql://192.168.179.5:3306/mynifi?characterEncoding=UTF-8&useSSL=false

- MySQL驱动类：com.mysql.jdbc.Driver
- MySQL jar包路径：需要提前在NiFI集群各个节点上创建对应目录并上传jar包。
- 连接mysql的用户名和密码。

通过以上配置好连接mysql如下：

![img](https://pic3.zhimg.com/80/v2-53dfa6473c0f2d260978f72f4c411dee_1440w.webp)

请在此添加图片描述

配置其他属性如下：

![img](https://pic1.zhimg.com/80/v2-e7093342da7bb36ee19b9643d38c78fc_1440w.webp)

请在此添加图片描述

## **二、配置“ConvertAvroToJSON”处理器**

此处理器是将二进制Avro记录转换为JSON对象，提供了一个从Avro字段到JSON字段的直接映射，这样得到的JSON将具有与Avro文档相同的层次结构。输出的JSON编码为UTF-8编码，如果传入的FlowFile包含多个Avro记录，则转换后的FlowFile是一个含有所有Avro记录的JSON数组或一个JSON对象序列(每个Json对象单独成行)。如果传入的FlowFile不包含任何记录，则输出一个空JSON对象。

关于“ConvertAvroToJSON”处理器的“Properties”配置的说明如下：

| 配置项                               | 默认值 | 允许值      | 描述                                                         |
| ------------------------------------ | ------ | ----------- | ------------------------------------------------------------ |
| JSON container options  （Json选择） | array  | none  array | 如何解析Json对象，none:解析Json将每个Json对象写入新行。array：解析到的json存入JsonArray一个对象 |
| Wrap Single Record  （数据库类型）   | false  | true  false | 指定解析到的空记录或者单条记录是否按照“JSON container options”配置包装对象。 |
| Avro schema  （表名）                |        |             | 如果Avro数据没有Schema信息，需要配置。                       |

配置步骤如下：

**1、创建“ConvertAvroToJSON”处理器**

![img](https://pic1.zhimg.com/80/v2-a0a61e8bef27916b100abdcfd6f4efdc_1440w.webp)

请在此添加图片描述

**2、配置“PROPERTIES”**

![img](https://pic4.zhimg.com/80/v2-23aa6d179029945fa920231c84d95383_1440w.webp)

请在此添加图片描述

**3、连接“QueryDatabaseTable”处理器和“CovertAvroToJSON”处理器**

![img](https://pic1.zhimg.com/80/v2-4d4dacc2846ea088f85d50c4ed46acb0_1440w.webp)

请在此添加图片描述

连接好两个处理器后，可以配置“Connection”为负载均衡方式传递数据：

![img](https://pic1.zhimg.com/80/v2-d17ddbda3a9159dd2012b09ae2069e50_1440w.webp)

请在此添加图片描述

## **三、配置“SplitJson”处理器**

该处理器使用JsonPath表达式指定需要的Json数组元素，将Json数组中的多个Json对象切分出来，形成多个FlowFile。每个生成的FlowFile都由指定数组中的一个元素组成，并传输到关系"split"，原始文件传输到关系"original"。如果没有找到指定的JsonPath，或者没有对数组元素求值，则将原始文件路由到"failure"，不会生成任何文件。

关于“SplitJson”处理器的“Properties”配置的说明如下：

| 配置项                                  | 默认值       | 允许值                          | 描述                                           |
| --------------------------------------- | ------------ | ------------------------------- | ---------------------------------------------- |
| JsonPath Expression  （Json表达式）     |              |                                 | 一个JsonPath表达式，它指定用以分割的数组元素。 |
| Null Value Representation（Null值表示） | empty string | empty string  the string 'null' | 指定结果为空值时的表示形式。                   |

配置步骤如下：

**1、创建“SplitJson”处理器**

![img](https://pic4.zhimg.com/80/v2-7a8a9920ff259ef8ba048a415b1fe3d7_1440w.webp)

请在此添加图片描述

**2、配置“PROPERTIES”**

![img](https://pic3.zhimg.com/80/v2-138318422dee616eb7e74cbaed85bc1e_1440w.webp)

请在此添加图片描述

**3、连接“ConvertAvroToJSON”处理器和“SplitJson”处理器**

连接后，连接关系选择“success”：

![img](https://pic2.zhimg.com/80/v2-08a8c008b9a6a934a233f5d538619a09_1440w.webp)

请在此添加图片描述

同时配置“ConverAvroToJSON”处理失败的数据自动终止：

![img](https://pic4.zhimg.com/80/v2-cb31df6f99d09e71e88e379760a4368b_1440w.webp)

请在此添加图片描述

![img](https://pic4.zhimg.com/80/v2-23172c49043ed6aa95ce7453165e0fb3_1440w.webp)

请在此添加图片描述

## **四、配置“PutHDFS”处理器**

该处理器是将FlowFile数据写入到HDFS分布式文件系统中。关于“PutHDFS”处理器的“Properties”主要配置的说明如下：

| 配置项                                       | 默认值 | 允许值                        | 描述                                                         |
| -------------------------------------------- | ------ | ----------------------------- | ------------------------------------------------------------ |
| Hadoop Configuration Resources（Hadoop配置） |        | none  array                   | HDFS配置文件，一个文件或者由逗号分隔的多个文件。不配置将在ClassPath中寻找‘core-site.xml’或者‘hdfs-site.xml’文件。 |
| Directory  （目录）                          |        |                               | 需要写入文件的HDFS父目录。如果目录不存在，将创建该目录。     |
| Conflict Resolution Strategy  (冲突解决)     | fail   | replace  ignore  fail  append | 指示当输出目录中已经存在同名文件时如何处理。                 |

配置步骤如下：

**1、创建“PutHDFS”处理器**

![img](https://pic1.zhimg.com/80/v2-47501289f519fbebacccb8e7cd5874a4_1440w.webp)

请在此添加图片描述

**2、配置“PROPERTIES”**

![img](https://pic1.zhimg.com/80/v2-1938f747d8f3192b3cc4d5ebb6a80b34_1440w.webp)

请在此添加图片描述

注意：以上需要在各个NiFi集群节点上创建“/root/test”目录，并且在该目录下上传hdfs-site.xml和core-site.xml文件。

**3、连接“SplitJson”处理器和“PutHDFS”处理器**

![img](https://pic3.zhimg.com/80/v2-d95fbf21ad0662520d7e5fd0ea199b26_1440w.webp)

请在此添加图片描述

同时设置“SplitJson”处理器中“failure”和“original”数据关系自动终止。

![img](https://pic1.zhimg.com/80/v2-d69d9e8f7a2b3065ee6e039d4c7f87ac_1440w.webp)

请在此添加图片描述

设置“PutHDFS”处理器“success”和“failure”数据关系自动终止：

![img](https://pic3.zhimg.com/80/v2-1f9f418552df36b839c81ce226a8ca3e_1440w.webp)

请在此添加图片描述

配置好的连接关系如下：

![img](https://pic3.zhimg.com/80/v2-a6de448632a4000a7a89716d4bd45ad6_1440w.webp)

请在此添加图片描述

## **五、运行测试**

### **1、在MySQL创建库“mynifi”,并且创建表“test1”，向表中插入10条数据**

```text
mysql> create database mynifi;
Query OK, 1 row affected (0.02 sec)

mysql> use mynifi;
Database changed

mysql> create table test1(id int,name varchar(255),age int );
Query OK, 0 rows affected (0.07 sec)

mysql> insert into test1 values (1,"zs",18),(2,"ls",19),(3,"ww",20),(4,"ml",21),(5,"tt",22)
```



### **2、首先启动“QueryDatabaseTable”处理器观察队列数据**

![img](https://pic3.zhimg.com/80/v2-930e27e97899f67d8f4a8043cac1bd6e_1440w.webp)

请在此添加图片描述

![img](https://pic4.zhimg.com/80/v2-ccc80c4a23652775115d4fcf16d93f8f_1440w.webp)

请在此添加图片描述

### **3、单独启动“ConvertAvroToJson”处理器观察队列数据**

![img](https://pic2.zhimg.com/80/v2-d25bf46faa5c3d05375c3874cbbdfd1d_1440w.webp)

请在此添加图片描述

### **4、单独启动“SplitJson”处理器观察队列数据**

![img](https://pic1.zhimg.com/80/v2-0d93b7d202b3da2012bfc03158604b98_1440w.webp)

请在此添加图片描述

### **5、单独启动“PutHDFS”处理器观察HDFS对应目录数据**

![img](https://pic4.zhimg.com/80/v2-07fd90669eb44cc0ca3ff09254dcd4af_1440w.webp)

请在此添加图片描述

查看数据：

![img](https://pic1.zhimg.com/80/v2-874400537324b80d2e78cb358352375c_1440w.webp)

请在此添加图片描述

**注意：**

- 如果在“QueryDatabaseTable”处理器中设置增属性“Maximum-value Columns”为id，那么每次查询都是大于id的增量数据。
- 如果想要存入HDFS文件为多行而不是一行，可以将“CovertAvroToJson”处理器属性“JSON container options”设置为none，直接解析Avro文件得到一个个json数据，然后直接连接“PutHDFS”处理器即可。

发布于 2023-03-05 10:51・IP 属地广东