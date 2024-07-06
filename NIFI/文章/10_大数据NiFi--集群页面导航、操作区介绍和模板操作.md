# 10_大数据NiFi--集群页面导航、操作区介绍和模板操作

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **集群页面导航、操作区介绍和模板操作**

## **一、导航、操作区介绍**

![img](https://pic4.zhimg.com/80/v2-4bd1af1346980dff7dcef8dbcf4739f7_1440w.webp)

注意：以上“Operate”选项卡中的启用和禁用只能对组件作用，不能作用在组上。启动和停止如果选择了组件或者组，那么是对对应的组或者组件进行启动或停止，如果不选择，将会启动/停止所有。

## **二、模板操作**

在NiFi中使用处理器设计复杂的数据流处理流程时,我们经常会发现我们将相同的处理器序列串在一起以执行某些任务。这种情况下,我们可以将“相同的流程”创建成模板达到复用目的，模板可以被认为是可重用的子流。

下面以“入门案例”中的处理器为例，来演示如何创建、导入、使用模板。

- **创建模板**

在画布中“shift+左键”选择要创建模板的组件，点击右键选择“create Template”或者在“Operate”操作区中选择创建模板。

![img](https://pic4.zhimg.com/80/v2-78cf6c08062572b30f517f6efb0a311b_1440w.webp)

输入模板名称及描述：

![img](https://pic1.zhimg.com/80/v2-b8049d2a2ab1c5f477a65e973d3fe350_1440w.webp)

- **查看模板**

![img](https://pic4.zhimg.com/80/v2-bd3eb6b1f13e2cf3d12169efb8f5590b_1440w.webp)

- **导入模板**

![img](https://pic4.zhimg.com/80/v2-cfdb1b7b0d81bad2d91fdcd25d4abbc7_1440w.webp)

- **使用模板**

![img](https://pic1.zhimg.com/80/v2-2a275bab3bded1c0de126bf06b0d30a4_1440w.webp)

发布于 2023-02-12 09:23・IP 属地广东