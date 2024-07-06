# 11_大数据NiFi--NiFi入门案例一

[![Lansonli](https://pic1.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi入门案例一**

需求：将A目录下实时产生的文件导入到指定的B目录下。

这里需要使用到的处理器是“GetFile”和“PutFile”，完成以上需求对“GetFile”和“PutFile”相关属性进行配置。

## **一、配置“GetFile”处理器**

### **1、拖拽“Processor”，搜索“GetFile”,创建处理器**

![img](https://pic4.zhimg.com/80/v2-84bf36e0f574127e2ab9f923edbffe2b_1440w.webp)

### **2、双击“GetFile”处理器面板，打开配置，选择“PROPERTIES”进行配置**

设置监控数据的目录，这里设置“/root/test/A”需要在集群中每个节点上预先创建好这个目录。

![img](https://pic2.zhimg.com/80/v2-4adab29bfcbd45753aaefc5385e93a79_1440w.webp)

点击“Apply”，信息如下：

![img](https://pic4.zhimg.com/80/v2-42db4cfe8111aa77d1703130ea83eb4b_1440w.webp)

以上警告信息时当前处理器没有连接下游的任何处理器，后期我们将要连接“PutFile”处理器。

关于以上“GetFile”处理器的“Properties”配置的说明如下：

| 配置项                                 | 默认值 | 允许值      | 描述                                                         |
| -------------------------------------- | ------ | ----------- | ------------------------------------------------------------ |
| Input Directory  （数据输入目录）      | [^.].* |             | 要提取文件的目录。                                           |
| File Filter  （文件过滤）              |        |             | 仅选择文件名与给定正则表达式匹配的文件。                     |
| Path Filter  （路径过滤）              |        |             | 当“Recurse Subdirectories”为true时，只扫描路径与给定正则表达式匹配的子目录。 |
| Batch Size  （批量大小）               | 10     |             | 每次迭代要拉入的最大文件数。                                 |
| Keep Source File  （保留源文件）       | false  | true  false | 如果为true,则文件在复制到“Content Repository”后不会被删除。默认为false。 |
| Recurse Subdirectories  （递归子目录） | true   | true  false | 是否递归从子目录获取文件。                                   |
| Polling Interval  （轮询间隔）         | 0 sec  |             | 设置执行目录列表之前需要等待多长时间。                       |
| Ignore Hidden Files  （忽略隐藏文件）  | true   | true  false | 是否忽略隐藏的文件。                                         |
| Minimum File Age  （最小文件年龄）     | 0sec   |             | 设置要提取文件必须的最小年龄，任何比这个时间短的文件(根据最后修改日期)都将被忽略。 |
| Maximum File Age  （最大文件年龄）     |        |             | 设置要提取文件必须的最大年龄，任何比这个时间大的文件(根据最后修改日期)都将被忽略。 |
| Minimum File Size  （最小文件大小）    | 0 B    |             | 设置要提取文件所必须的最小大小。                             |
| Maximum File Size  （最大文件大小）    |        |             | 设置要提取文件所必须的最大大小。                             |

## **二、配置“PutFile”处理器**

### **1、拖拽“Processor”，搜索“GetFile”,创建处理器**

![img](https://pic3.zhimg.com/80/v2-0389626860b3989ba403cc6d39ca26ea_1440w.webp)

### **2、双击“PutFile”处理器面板，打开配置，选择“PROPERTIES”进行配置**

设置数据流入当前处理器后写往的目录。

![img](https://pic1.zhimg.com/80/v2-c2de3551f96b5601cef54504d52fed04_1440w.webp)

关于以上“PutFile”处理器的“Properties”配置的说明如下：

| 配置项                                       | 默认值 | 允许值                | 描述                                                         |
| -------------------------------------------- | ------ | --------------------- | ------------------------------------------------------------ |
| Directory  （数据写出目录）                  |        |                       | 要提取文件的目录。可以使用表达式，例如：/x1/x2/${path}。     |
| Conflict Resolution Strategy  (解决冲突策略) | fail   | replace  ignore  fail | 指示当输出目录中已经存在同名文件时应该如何处理。             |
| Create Missing Directories  （创建缺失目录） | true   |                       | 如果为true，则将创建缺少的目标目录。如果为false，则流文件发送失败。 |
| Maximum File Count  （最大文件数）           |        |                       | 指定输出目录中可以存在的文件的最大数量。                     |
| Last Modified Time  （上次修改时间）         |        |                       | 将输出文件的lastModifiedTime设置为此属性的值，格式必须为yyyy-MM-dd'T'HH:mm:ssZ，可以使用表达式，例如${file.lastModifiedTime}。 |
| Permissions  （权限）                        |        | true  false           | 将输出文件的权限设置为此属性的值，格式必须是UNIX rwxrwxrwx，用-来代替被拒绝的权限(例如rw-r--r--)或八进制数字(例如644)，可以使用表达式，例如${file.permissions}。 |
| Owner                                        |        | true  false           | 将输出文件的所有者设置为此属性的值，可以使用表达式语言，比如${file.owner}。注意:在许多操作系统上，Nifi必须以超级用户的身份运行，才能拥有设置文件所有者的权限。 |
| Group                                        |        |                       | 将输出文件上的组设置为此属性的值，可以使用表达式语言，例如${file.group}。 |

### **3、连接“GetFile”与“PutFile”处理器**

![img](https://pic2.zhimg.com/80/v2-9115d46f085614ccd40e20ba0deb5a55_1440w.webp)

经过连接处理器后，“PutFile”处理器还有警告如下：

![img](https://pic2.zhimg.com/80/v2-d696fe5fc2b996a7a8f5e28466784c7d_1440w.webp)

双击“PutFile”,配置“SETTINGS”：

![img](https://pic1.zhimg.com/80/v2-1f94dd686d662e5f30215ac71f3eeba4_1440w.webp)

## **三、测试**

在画布空白处点击右键，选择“Start”将“GetFile”和“PutFile”都启动起来，在Linux中node1、node2、node3节点任意一台节点“/root/test/A”目录下写入以下数据，也可以将现有文件移动到对应的目录下：

```text
[root@node1 ~]# echo "aaa" > /root/test/A/a1.txt
[root@node2 ~]# echo "bbb" > /root/test/A/a2.txt
[root@node3 ~]# echo "ccc" > /root/test/A/a3.txt
```

刷新WebUI页面，显示数据成功被存入“/root/test/B”目录中。

![img](https://pic3.zhimg.com/80/v2-42924dcc2ae3c8f452149b6a3310d36e_1440w.webp)

在对应的node1、node2、node3节点上我们可以看到“/root/test/B”目录中有对应的数据。如果要停止以上处理器，可以在画布面板空白处右键选择“stop”将所有的处理器停止。

注意：数据写出的目录可以不需要提前在对应节点上创建，默认是可以自动创建，可以通过“Create Missing Directories”属性配置。如果写入的数据文件名称相同，这时可以配置属性“Conflict Resolution Strategy”为“replace”替换。

发布于 2023-02-18 08:19・IP 属地广东