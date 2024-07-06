# 12_大数据NiFi--集群页面组操作

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **集群页面组操作**

在NiFi中重新创建“GetFile”和“PutFile”处理器,形成新的DataFlow，并且各自重新命名：

![img](https://pic1.zhimg.com/80/v2-227fa680681a09becae9083a7ea66f00_1440w.webp)

以上重新创建新的DataFlow或者导入外部模板生成的DataFlow，都会与页面中已经存在的处理器“混”在一起，为了便于理解和维护DataFlow,可以创建组来将不同的DataFlow放入不同的组。

拖拽“Process Group”，创建新的组，并命名为“FirstGroup”:

![img](https://pic3.zhimg.com/80/v2-bdaa18ba45b58a8359347953075a2d5a_1440w.webp)

选中第一个DataFlow，拖入到“FristGroup”组中：

![img](https://pic2.zhimg.com/80/v2-f4d2a171d8adcb4bf60250dea87fc501_1440w.webp)

也可以“Shift+鼠标左键”选中要放入同一个组的组件，右键选择“Group”创建新的组：

![img](https://pic1.zhimg.com/80/v2-9faff56980cd3cd84e21d9c09795a370_1440w.webp)

最终页面形成两个组。

![img](https://pic3.zhimg.com/80/v2-8894a8fa91781b724f89249ac5576fca_1440w.webp)

在对应的组上双击可以进入一个组，可以在组内继续创建组形成嵌套组，或者将组内的组件移动到组外，进入组之后在左下角会显示组的层级关系。

![img](https://pic4.zhimg.com/80/v2-6bf6646e86574c0bf78310aaa286ba2f_1440w.webp)

发布于 2023-02-20 23:26・IP 属地广东