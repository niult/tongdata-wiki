# 15_大数据NiFi--NiFi入门案例二

[![Lansonli](https://pica.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi入门案例二**

需求：随机生成一些测试数据集，对生成的数据进行正则匹配，对匹配后的数据进行输出到外部文件中。以上需要用到的“GenerateFlowFile”、“ReplaceText”、“PutFile”处理器。

## **一、配置“GenerateFlowFile”处理器**

这个处理器可以生成随机的FlowFile数据或者生成自定义内容的FlowFile。多用于负载测试和模拟生成数据测试。

### **1、拖拽“Processor”在弹框中输入“GenerateFlowFile”**

![img](https://pic3.zhimg.com/80/v2-0670e09f4719f94f0c9d76e29e3f864e_1440w.webp)



### **2、配置“GenerateFlowFile”生成FlowFile**

打开“GenerateFlowFile”配置，找到“PROPERTIES”选项进行配置：

![img](https://pic4.zhimg.com/80/v2-d5d4b44755fbced667e6900972af0e7b_1440w.webp)

关于“GenerateFileFile”的“PROPERTIES”配置选项解释如下：

| 配置项                           | 默认值 | 允许值       | 描述                                                         |
| -------------------------------- | ------ | ------------ | ------------------------------------------------------------ |
| File Size  （文件大小）          | 0 B    |              | 生成每个FlowFile文件的大小。                                 |
| Batch Size  （批次大小）         | 1      |              | 每次生成几个FlowFile。                                       |
| Data Format  （数据格式）        | Text   | Binary  Text | 指定生成的数据是文本还是二进制文件。                         |
| Unique FlowFiles（唯一FlowFile） | false  | true  false  | 如果为true,每次生成的FlowFile独一无二，如果为false，每个FlowFile随机内容相同，吞吐量大。 |
| Custom Text  （自定义文本）      |        |              | 自定义生成文本内容。需要将“Data Format”设置为Text并且将“Unique FlowFiles”设置为false，这时生成的文件大小不定，将忽略设置的“File Size” |
| Character Set  （字符编码）      | UTF-8  |              | 指定生成FlowFile的编码。                                     |
| Mime Type  （mime.type值）       |        |              | 设置自带属性“mime.type”的Value值。                           |

“GenerateFlowFile”默认调度会不间断运行产生数据，为了方便后期测试，这里设置好“PROPERTIES”配置后，还需要配置“SCHEDULING”中“Run Schedule”，当产生数据后，每隔10s调度一次，目的是产生数据慢一些，方便后期看到结果。

![img](https://pic1.zhimg.com/80/v2-9df4f99eed44d3f98a06caf185833278_1440w.webp)

## **二、配置“ReplaceText”处理器**

“ReplaceText”处理器会替换正则表达式匹配到的FlowFile中的内容，生成新的FlowFile内容。

### **1、拖拽“Processor”在弹框中输入“GenerateFlowFile”**

![img](https://pic2.zhimg.com/80/v2-09fa05677d52b9c93b3d26bc29378ead_1440w.webp)



### **2、配置“ReplaceText”处理器**

将接收“GenerateFlowFile”处理器生成的“hello world”数据，替换其中的“world”为“nifi”。

![img](https://pic3.zhimg.com/80/v2-58fd734985d725a8b0a8edc8d97376d2_1440w.webp)

关于“ReplaceText”的“PROPERTIES”配置选项解释如下：

| 配置项                                       | 默认值        | 允许值                                                      | 描述                                                         |
| -------------------------------------------- | ------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| Search Value  （正则匹配值）                 | (?s)(^.*$)    |                                                             | 对FlowFile内容匹配的正则表达式。仅用于“Regex Replace”和“Literal Replace”替换策略。 |
| Replacement Value（替换的值）                | $1            |                                                             | 使用"Replacement Strategy"策略时插入的值。$1表示正则表达式匹配的第1个值。 |
| Character Set  （编码）                      | UTF-8         |                                                             | 指定编码字符集。                                             |
| Maximum Buffer Size  （缓冲区数据量最大值）  | 1 M           |                                                             | 指定要缓冲的最大数据量(每个文件或每行，取决于计算模式)，以便应用替换。“评估模式”如果选择了“Entire Text”，并且FlowFile大于这个值，那么FlowFile将被路由到“failure”；在“Line-by-Line”模式下，如果一行文本比这个值大，那么FlowFile将被路由到“failure”。默认值为1 MB，主要用于“Entire Text”模式。在“Line-by-Line”模式中，建议使用8 KB或16 KB这样的值。如果将“替换策略”设置为以下其中之一:Append、Prepend、Always Replace，则忽略该值。 |
| Replacement Strategy  （替换策略）           | Regex Replace | Prepend Append Regex Replace Literal Replace Always Replace | 指定如何替换FlowFile内容的策略。                             |
| Evaluation Mode（评估模式）                  | Line-by-Line  | Line-by-Line Entire text                                    | 对每一行单独进行"替换策略"(Line-by-Line)；或将整个文件缓冲到内存中(Entire text)，然后对其进行"替换策略"。 |
| Line-by-Line Evaluation Mode（行的评估模式） | ALL           | All First-Line Last-Line Except-First-Line Except-Last-Line | Line-by-Line“评估模式”下：  (ALL)默认是对FlowFile中所有行使用“替换策略”。  (First-Line):只对第一行使用“替换策略”。  (Last-Line):只对最后一行使用“替换策略”。  (Except-First-Line):除了第一行之外使用“替换策略”。  (Except-Last-Line):除了最后一行外使用“替换策略”。 |

- Prepend
- Append
- Regex Replace
- Literal Replace
- Always Replace
  指定如何替换FlowFile内容的策略。 Evaluation Mode（评估模式） Line-by-Line
- Line-by-Line
- Entire text
  对每一行单独进行"替换策略"(Line-by-Line)；或将整个文件缓冲到内存中(Entire text)，然后对其进行"替换策略"。 Line-by-Line Evaluation Mode（行的评估模式） ALL
- All
- First-Line
- Last-Line
- Except-First-Line
- Except-Last-Line
  Line-by-Line“评估模式”下： (ALL)默认是对FlowFile中所有行使用“替换策略”。 (First-Line):只对第一行使用“替换策略”。 (Last-Line):只对最后一行使用“替换策略”。 (Except-First-Line):除了第一行之外使用“替换策略”。 (Except-Last-Line):除了最后一行外使用“替换策略”。

**以上表格中“Search Value”默认值“(?s)(^.\*$)”解释如下：**

- (?s)表示开启单行模式。
- (^.*$)中,^表示正则开始。
- .表示匹配换行符之外的任何字符。
- *表示匹配前面的子表达式0或者多次。
- $表示正则表达式结束。

**以上表格中“Replacement Strategy”的可用值解释如下：**

- Prepend：在流文件的开头或每行的开头插入替换值(取决于评估模式)。当"Evaluation Mode-评估模式"设置为Line-by-Line"时，值将被添加到每一行开头，设置为"Entire Text"时，该值将被添加到整个文本之前。
- Append：在流文件的末尾或每行的末尾插入替换值(取决于评估模式)。当"评估模式"设置为Line-by-Line"时，值将被添加到每一行开头，设置为"Entire Text"时，该值将被添加到整个文本之前。
- Regex Replace：使用"Replacement Value"替换所有正则表达式匹配值，"Replacement Value"可以引用正则表达式匹配的值，使用、1、2等来引用。
- Literal Replace:当"Search Value"值为一个搜索值时，使用"Replacement Value"替换值替换匹配项。
- Always Replace:总是替换整个行或FlowFile的整个内容(取决于"Evaluation Mode（评估模式）"属性的值)，不会搜索任何值。当选择此策略时，"Search Value"属性将被忽略。

## **三、配置“PutFile”处理器**

关于“PutFile”处理器的创建及配置参数参照案例一，这里直接给出“PutFile”处理器的配置，将替换后的FlowFile写入外部路径中“/root/test/matchFile”：

![img](https://pic3.zhimg.com/80/v2-ae6083a19d8a506ad841f7ea922b3efe_1440w.webp)



![img](https://pic1.zhimg.com/80/v2-41a2f7bfc2a173cc8082349bc78ae0e0_1440w.webp)

## **四、连接各个处理器，并且启动测试**

在连接“ReplaceText”处理器与“PutFile”处理器时，需要设置连接的配置关系，当“ReplaceText”处理器将匹配成功的数据写出到“PutFile”处理器：

![img](https://pic1.zhimg.com/80/v2-aa176c128eacb14a3a0be953d9d2cddc_1440w.webp)



设置当“ReplaceText”处理器匹配失败FlowFile自动终止关系：

![img](https://pic4.zhimg.com/80/v2-8055cce647484b0e96c73a3f2f5d6d67_1440w.webp)



完整的连接关系如下：

![img](https://pic2.zhimg.com/80/v2-c5b17e8481c6132d11e8b5c35f4e7189_1440w.webp)



启动“GenerateFlowFile”处理器后：

![img](https://pic4.zhimg.com/80/v2-0152df0963323c647bda2c12ddb6dad3_1440w.webp)

同时查看“Data Provenance”中数据如下：

![img](https://pic1.zhimg.com/80/v2-8715ce5b75b01ab4030d01125644798c_1440w.webp)



启动“ReplaceText”处理器，查看处理的数据：

![img](https://pic1.zhimg.com/80/v2-5efe03edc706e4488811b49fcb6e7058_1440w.webp)



![img](https://pic4.zhimg.com/80/v2-26b7039e4d9f7218a7cadfd3ab51a77f_1440w.webp)

启动“PutFile”处理器，NiFi集群对应的每个节点上都生成对应的数据：

![img](https://pic1.zhimg.com/80/v2-8c667fdb1ecf58ef8c0b55bcdf301a54_1440w.webp)

查看数据结果：

![img](https://pic1.zhimg.com/80/v2-b0c96e8b217de948492526aabae8ff44_1440w.webp)

发布于 2023-02-25 11:58・IP 属地广东