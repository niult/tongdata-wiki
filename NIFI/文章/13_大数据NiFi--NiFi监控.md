# 13_大数据NiFi--NiFi监控

[![Lansonli](https://pica.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi监控**

在组件工具栏下的NiFi屏幕顶部附近有一个条形,称为状态栏。它包含一些关于NiFi当前健康状况的重要统计数据：活动线程的数量可以指示NiFi当前的工作状态,排队统计数据表示当前在整个流程中排队的FlowFile数量以及这些FlowFiles的总大小。

画布上的每个处理器,进程组(Group)和远程进程组都提供了有关组件处理了多少数据的若干统计信息。这些统计信息提供有关在过去五分钟内处理了多少数据的信息。这是一个滚动窗口,允许我们查看处理器消耗的FlowFiles数量,以及处理器发出的FlowFiles数量。处理器之间连接还会显示当前排队的数据条目数。

![img](https://pic4.zhimg.com/80/v2-ecbf5a5f013a7457208985f94f5e75d7_1440w.webp)

对于每个处理器监控信息如下：

![img](https://pic2.zhimg.com/80/v2-6795ad6c34c709ac5331dde2b9b87f1d_1440w.webp)

## **一、处理器状态指示有如下几种情况**

![img](https://pic3.zhimg.com/80/v2-fd19314c65f4aada12cfdfe1911b2a46_1440w.webp)

处理器正在运行。

![img](https://pic1.zhimg.com/80/v2-ea5e613c72f1e535e7cca295ee518730_1440w.webp)

处理器已停止。

![img](https://pic3.zhimg.com/80/v2-590f764ca7f0c2e7a11aebd31e5bf47e_1440w.webp)

处理器无效，鼠标放在图标上显示无效原因。



![img](https://pic2.zhimg.com/80/v2-b8596f8b711240d4fd86c80d6b560529_1440w.webp)

处理器已禁用。需要解除禁用才能正常启动、停止处理器。

可以通过“公告指示”来查看事件处理日志信息，默认级别是“WARN”级别，可以在处理器配置中设置，当有对应级别的事件信息产生时，才会显示。

## **二、对于每个组的监控情况如下**

![img](https://pic2.zhimg.com/80/v2-6ad38198acd9d25bcdcba275b38f7379_1440w.webp)

“组件信息统计”中图表含义如下：

![img](https://pic3.zhimg.com/80/v2-4f2bee6f4e5cf8022c71e6400e247bd6_1440w.webp)

配置的远程NiFi实例传输个数。

![img](https://pic1.zhimg.com/80/v2-5965e0e837c14e1256a4675bedc330ac_1440w.webp)

禁用的远程NiFi实例传输个数。

![img](https://pic3.zhimg.com/80/v2-fd19314c65f4aada12cfdfe1911b2a46_1440w.webp)

组内正在运行的处理器个数。

![img](https://pic1.zhimg.com/80/v2-ea5e613c72f1e535e7cca295ee518730_1440w.webp)

组内已经停止的处理器个数。

![img](https://pic3.zhimg.com/80/v2-590f764ca7f0c2e7a11aebd31e5bf47e_1440w.webp)

组内无效处理器数量。

![img](https://pic2.zhimg.com/80/v2-b8596f8b711240d4fd86c80d6b560529_1440w.webp)

组内已禁用处理器数量。

可以通过点击“菜单”选择“Summary”摘要信息，查看集群中各个处理器的详细信息：

![img](https://pic1.zhimg.com/80/v2-f90e602a882d72b414d3e9e38c40712c_1440w.webp)

点击上图中“查看处理器处理数据历史”可以弹出：

![img](https://pic4.zhimg.com/80/v2-7465fbc8361f7d0d747e812c7b540eaf_1440w.webp)

可以通过点击“菜单”选择“Node Status History”查看集群中各个节点的内存使用情况。

![img](https://pic2.zhimg.com/80/v2-139cc109d99452c1904a1c22b7e4e989_1440w.webp)

发布于 2023-02-21 09:19・IP 属地广东