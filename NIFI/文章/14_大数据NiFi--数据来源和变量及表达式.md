# 14_大数据NiFi--数据来源和变量及表达式

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **数据来源和变量及表达式**

## **一、数据来源**

NiFi对其摄取的每个数据保存明细。当数据通过系统处理并被转换,路由,拆分,聚合和分发到其他端点时,这些信息都存储在NiFi的Provenance Repository中。为了搜索和查看此信息,我们可以从全局菜单中选择数据源(Data Provenance)，也可以在对应的处理器上右键选择“View data provenance”进行查看。

![img](https://pic3.zhimg.com/80/v2-fb47781eea93a207aad5120e096816d2_1440w.webp)

![img](https://pic1.zhimg.com/80/v2-1258b6cba0043e0162b7918dac2e7e98_1440w.webp)

以上表格默认显示1000个最近的事件信息。当点击每个事件前面的“i”标记时，可以查看当前事件的详细信息：

![img](https://pic2.zhimg.com/80/v2-07a5ef78e7f8ec6ac30b809780718d7d_1440w.webp)

还可以通过点击“Lineage”查看FlowFile的执行流程关系：

![img](https://pic1.zhimg.com/80/v2-165edae6b7ef4f448e20cdade1270080_1440w.webp)

左下角的滑块允许我们查看这些事件发生的时间。通过左右滑动,我们可以看到哪些事件花费了较长的时间,这样我们可以分析瓶颈,得知哪些节点需要更多资源,例如配置处理器的并发任务数。

## **二、变量及表达式**

FlowFile由两个主要部分组成：内容和属性,我们可以在一些情况下引用FlowFile对应的属性，这里就可以使用表达式来获取对应的属性，甚至有时候我们还需要自定义一些属性值方便灵活处理数据流，这就是定义变量。

### **定义变量**

在画布空白处右键选择“Variables“：

![img](https://pic4.zhimg.com/80/v2-e91526017b2aeeccec5617da63a1cdd3_1440w.webp)

在弹出的框中添加变量：

![img](https://pic4.zhimg.com/80/v2-42365995069ea3077f22aacf8cb6a137_1440w.webp)

点击“OK”后，弹框填写“value”值:

![img](https://pic4.zhimg.com/80/v2-9051e95785142968985e8bc1a34e29af_1440w.webp)

按照以上方法继续添加“output_path”变量对应value为“/root/test/B”，添加完成之后如下：

![img](https://pic3.zhimg.com/80/v2-74df2a451734d19cc5c1e44aff7cb126_1440w.webp)

以上添加的变量是在主面板上添加，主面板上添加的变量可以在各个组内使用，也可以在每个组内添加变量，如果变量名称冲突，在组内定义的变量对应的值生效。

### **使用表达式**

表达式用来引用DataFlow属性或者引用定义好的变量，方便在创建和配置数据流时使用他们的值。NiFi表达式语言始终以符号"{{{filename}将返回filename 属性的值。

在稍微复杂一点的示例中，我们可以改为返回对此值的操作。例如，我们可以通过调用toUpper函数来返回文件名的全部大写版本 。在这种情况下，我们引用该属性，然后使用该函数来操纵该值。其中��������:�������()。在这种情况下，我们引用该��������属性，然后使用该�������函数来操纵该值。其中{filename:toUpper():equals('HELLO.TXT')} 判断文件名是否是某个值，函数数量没有限制，关于更多函数参照官网：

**[http://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html#functions](https://link.zhihu.com/?target=http%3A//nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html%23functions)[1]**

在演示将目录A下的数据文件导入到目录B下案例时，B目录是手动写死的，这里我们定义好了变量可以直接在处理器属性中引用值。进入到“First Group”中，可以右键选择“Variables”变量查看到在上一层组定义的变量，这里我们直接配置“GetFile”和“PutFile”的路径如下：

![img](https://pic4.zhimg.com/80/v2-9a63987d2977ed70d6547a69a75578ab_1440w.webp)

![img](https://pic4.zhimg.com/80/v2-c2f793b6d442880a43b52182c1d0627b_1440w.webp)

经过以上设置，清空node1、node2、node3节点”/root/test/A”和”/root/test/B”目录,每个节点重新创建“/root/test/A”目录，并复制粘贴一些文件到该目录下，启动“GetFile”和“PutFile”处理器，可以观察WebUI页面，数据被处理，在对应的“/root/test/B”目录下有数据。

注意，在处理器“Properties”页面中有很多属性，有些属性值不支持表达式引用值，可以在对应的属性上点击“?”符号来查看是否支持表达式：

![img](https://pic1.zhimg.com/80/v2-634d861cace8d7659f4ada96307a1ee4_1440w.webp)

### **参考资料**

[1] [http://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html#functions:](https://link.zhihu.com/?target=http%3A//nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html%23functions%3A) *[http://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html#functions](https://link.zhihu.com/?target=http%3A//nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html%23functions)*

发布于 2023-02-24 08:02・IP 属地广东