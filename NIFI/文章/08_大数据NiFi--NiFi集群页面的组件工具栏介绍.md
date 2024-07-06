# 08_大数据NiFi--NiFi集群页面的组件工具栏介绍

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi集群页面的组件工具栏介绍**

## **一、处理器（Processor）**

![img](https://pic3.zhimg.com/80/v2-a5ae6b11fade63ecb6c56ff28949d4ba_1440w.webp)

处理器是最常用的组件,因为它负责数据的流入,流出,路由和操作，有许多不同类型的处理器，将处理器拖动到画布上时,会向用户显示一个对话框,以选择要使用的处理器类型。

## **二、数据输入端口/输出端口（Input Port/Output Port）**

![img](https://pic4.zhimg.com/80/v2-af0a83ca1fd75644a6d3f2d27dc4d947_1440w.webp)

![img](https://pic2.zhimg.com/80/v2-fad6d72070736de756cca770f337a44d_1440w.webp)

虽说是数据流输入点/流出点，但是并不是整体数据流的起点。它是作为组与组之间的数据流连接的传入点与输出点。

## **三、进程组(Process Group)**

![img](https://pic4.zhimg.com/80/v2-9825d87895b7f42f02ae15fe199c570f_1440w.webp)

进程组可用于对一组组件进行逻辑分组,以便更容易理解和维护DataFlow，组相当于系统中的文件夹，作用就是使数据流的各个部分看起来更工整，思路更清晰，不至于从头到尾一条线阅读起来十分不方便。

## **四、远程进程组(Remote Process Group)**

![img](https://pic2.zhimg.com/80/v2-2b432253ac4915227e445c54a13e6485_1440w.webp)

添加远程的组，与进程组类似。可以连接其他NiFi集群。

## **五、聚合(Funnel)**

![img](https://pic1.zhimg.com/80/v2-bb8c3621f00a1a26a0d234a07051fff4_1440w.webp)

可以将来自多个Connections连接的数据合并到一个Connection中。

## **六、模板（Template）**

![img](https://pic2.zhimg.com/80/v2-cddaef1c753abaede2bf174472686901_1440w.webp)

可以将若干组件组合在一起以形成更大的组，从该组创建数据流模版。这些模板也可以导出为XML并导入到另一个NiFi实例中，从而可以共享这些组。

## **七、标签（Label）**

![img](https://pic1.zhimg.com/80/v2-9b4bdbfd31c8ae9534fdbc1c5b90da18_1440w.webp)

标签用于为数据流的各个部分提供文档说明，可放置在画布空白处，写上备注信息。

发布于 2023-02-08 09:25・IP 属地广东