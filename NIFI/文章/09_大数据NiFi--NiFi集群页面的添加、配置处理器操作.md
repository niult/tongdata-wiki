# 09_大数据NiFi--NiFi集群页面的添加、配置处理器操作

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi集群页面的添加、配置处理器操作**

下面以GetFile Processor为例来添加配置处理器：

## **一、从工具栏中拖入Processor，在弹出的面板中搜索GetFile,然后点击“ADD”添加Processor**

![img](https://pic4.zhimg.com/80/v2-b42323cc0f7f35d4b846c6d737a1c22f_1440w.webp)

![img](https://pic2.zhimg.com/80/v2-e0d485ca74ff5a50313435fc1b25a3e1_1440w.webp)

在生成的GetFile processor上右键，弹出可操作选项：

![img](https://pic2.zhimg.com/80/v2-6ad61a8bfe92a3b247125b3dd3a27c21_1440w.webp)

**对以上操作选项的解释如下：**

| 操作选项                                    | 功能描述                                                     |
| ------------------------------------------- | ------------------------------------------------------------ |
| Configure(配置)                             | 此选项允许用户建立或更改处理器的配置,也可以在对应的Processor上双击打开。 |
| Disable/Enable  （启用/禁用）               | 此选项允许用户启用或禁用处理器,具体取决于处理器的当前状态。  |
| View data provenance  （查看数据来源）      | 此选项显示NiFi数据来源表,其中包含有关通过该处理器路由的FlowFiles的数据出处事件的信息。 |
| View status history  （查看历史状态）       | 此选项打开处理器随时间的图形统计历史信息。                   |
| View usage  （查看用法）                    | 此选项跳转到处理器的使用文档。                               |
| View connections→Upstream（查看上游连接）   | 此选项允许用户查看和跳转到进入处理器的上游连接。             |
| View connections→Downstream（查看下游连接） | 此选项允许用户查看和跳转到处理器的下游连接。                 |
| Center in view  （放在视图中心）            | 此选项将当前处理器放在面板中心。                             |
| Change color（更改颜色）                    | 此选项允许用户更改处理器的颜色。                             |
| Group（添加到组）                           | 此选项将处理器添加到组。                                     |
| Create template  （创建模板）               | 此选项允许用户从所选处理器创建模板。                         |
| Copy（复制）                                | 复制当前处理器。                                             |
| Delete（删除）                              | 删除当前处理器。                                             |

## **二、配置处理器**

鼠标双击处理器或者选择以上“Configure”，打开配置处理器选项，配置分为四个部分：SETTINGS,SCHEDULING,PROPERTIES,COMMENTS。

**Settings-设置：**

![img](https://pic1.zhimg.com/80/v2-163b0a1eb0889fe925a6ae54d08c3384_1440w.webp)

**Scheduling-调度：**

![img](https://pic1.zhimg.com/80/v2-9e0cb9980d83b3a140bf12b26c8781ec_1440w.webp)

**Properties-配置：**

![img](https://pic2.zhimg.com/80/v2-b9ef2c9fafd2835ee40a5ac4d020803d_1440w.webp)

**Comments-注释：**

![img](https://pic3.zhimg.com/80/v2-22923736cd13b98487860376f5ee2d22_1440w.webp)

发布于 2023-02-10 06:12・IP 属地广东