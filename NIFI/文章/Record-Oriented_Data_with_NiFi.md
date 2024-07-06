## 【Apache NIFI 翻译】Record-Oriented Data with NiFi

南方有乔木

于 2021-02-23 17:27:09 发布

【Apache NIFI】Record-Oriented Data with NiFi
原文档地址：https://blogs.apache.org/nifi/



### INTRO - THE WHAT

许多公司和组织正在使用Apache NiFi来满足其数据分发需求。 NiFi的优势之一是该框架与数据无关。不管您正在处理哪种类型的数据。有用于处理JSON，XML，CSV，Avro，图像和视频以及其他几种格式的处理器。还有几种通用处理器，例如RouteText和CompressContent。数据可以是数百个字节，也可以是多个千兆字节。这使NiFi成为从外部来源提取数据，路由，转换和聚合，并最终将其运送到最终目的地的强大工具。

    尽管这种处理任意数据的能力非常强大，但我们经常看到用户使用面向记录的数据（record-oriented data）。也就是说，大量的小型“记录（records）”或“消息（messages）”或“事件（events）”。每个人都有自己的方式来谈论这些数据，但是我们都是同一回事。很多非常小的，通常是结构化的信息。该数据有多种格式。最常见的是CSV，JSON，Avro和日志数据。
    
    NiFi使许多任务变得容易，但有些常见任务我们可以做得更好。因此，在NiFi 1.2.0版中，我们发布了一组新的处理器和控制器服务，用于处理面向记录的数据（record-oriented data）。新的处理器配置有记录读取器（Record Reader）和记录写入器（Record Writer ）控制器服务。有JSON，CSV，Avro和日志数据的读取器。有JSON，CSV和Avro的写入器，以及允许用户输入自由格式文本的写入器。这可以用于以日志格式（如读入）或任何其他自定义文本格式写出数据。

### THE WHY

使用这些新的处理器构建流程可以更轻松地处理这些数据。这也意味着我们可以构建可以接受任何数据格式的处理器，而不必担心解析和序列化逻辑。这种方法的另一个主要优点是，我们能够使FlowFiles更大，每个FlowFiles由多个记录组成，从而获得更好的性能。

### THE HOW - EXPLANATION

​    为了理解数据，记录读取器（Record Reader）和写入器（Record Writer ）需要了解与数据关联的模式（schema）。一些读取器（例如Avro Reader）允许从数据本身读取模式（schema）。该方案也可以作为FlowFile属性包括在内。但是，在大多数情况下，将从模式注册表（Schema Registry）中按名称查找它。在此版本的NiFi中，存在两种模式注册表（Schema Registry）实现：基于Avro的模式注册表服务和用于外部Hortonworks Schema Registry的注册表服务。

    首先配置所有这些功能可能会有些困难。但是，一旦完成一次或两次，它就会变得非常快速且易于配置。在这里，我们将逐步介绍如何设置本地Schema Registry服务，配置Record Reader和Record Writer，然后开始使用一些功能非常强大的处理器。对于本文，我们将使流程保持简单。我们将从文件中提取一些CSV数据，然后使用Record Reader和Record Writer将数据转换为JSON，然后将其写入新目录。

### THE HOW - TUTORIAL

为了开始读取和写入数据，如上所述，我们将需要一个Record Reader 服务。我们想解析CSV数据并将其转换为JSON数据。因此，我们需要一个CSV Reader 和一个JSON Writer。因此，我们将从单击“操作”面板上的“设置”图标开始。这将使我们能够开始配置控制器服务。当单击右上角的“添加”按钮时，我们会看到Controller Services的许多选项：



    由于我们知道我们要读取CSV数据，因此可以在对话框右上角的“过滤器”框中键入“ CSV”，这样可以很好地缩小选择范围：



    我们将选择添加CSVReader服务，然后配置Controller Service。在“属性”选项卡中，我们可以设置许多不同的属性：



    幸运的是，这些属性中的大多数具有适用于大多数情况的默认值，但是如果不是逗号，则可以选择要使用的定界符。您可以选择是否跳过第一行，将其视为标题，等等。对于我而言，我将"Skip Header Line" 属性设置为“ true”，因为我的数据包含我不想作为record处理标题行。但是，第一个属性非常重要。"Schema Access Strategy"用于指导读者如何获取模式（ schema）。默认情况下，它设置为 "Use String Fields From Header." （“使用标题中的字符串字段”）。但是，由于我们还将要写入数据，因此无论如何我们都必须配置一个模式（ schema）。因此，对于此demo，我们将将此策略更改为"Use 'Schema Name' Property." （“使用'Schema Name'属性”）。这意味着我们将从架构注册表（Schema Registry.）中查找schema。结果，我们现在将不得不创建我们的Schema Registry.）。如果单击 "Schema Registry" 属性，则可以选择“创建新服务...”：



    我们将选择创建一个AvroSchemaRegistry。在这里需要特别注意的是，我们正在读取CSV数据并写入JSON数据-那么为什么要使用Avro Schema Registry？因为此架构注册表（Schema Registry）允许我们使用Apache Avro Schema Fomat来传达schema，但它并不意味着有关正在读取的数据格式的任何信息。之所以使用Avro格式，是因为它已经是一种众所周知的存储数据模式的方式。
    
    添加我们的Avro Schema Registry后，我们可以对其进行配置，并在“属性”选项卡中看到它根本没有属性。我们可以通过添加一个新的用户定义属性（通过单击右上角的“添加” /“加号”按钮）来添加模式。通过使用它作为属性的名称，我们将为schema 指定名称“ demo-schema”。然后我们可以在schema中输入或粘贴我们自己写好的schema。对于那些不熟悉Avro schema的人，它是JSON格式的表示形式，其语法如下所示：

｛
  "name": "recordFormatName",
  "namespace": "nifi.examples",
  "type": "record",
  "fields": [
    { "name": "id", "type": "int" },
    { "name": "firstName", "type": "string" },
    { "name": "lastName", "type": "string" },
    { "name": "email", "type": "string" },
    { "name": "gender", "type": "string" }
  ]
}
    在这里，我们有一个简单的 schema，类型为 "record."。通常情况就是这样，因为我们需要多个字段（ fields）。然后，我们指定我们拥有的所有字段。有一个名为“ id”的字段，类型为“ int”，所有其他字段的类型为“ string”。有关更多信息，请参见Avro Schema文档。现在，我们已经配置了schema！我们现在可以启用我们的控制器服务。

    现在，我们还可以添加JsonRecordSetWriter控制器服务。配置此服务时，我们会看到一些熟悉的选项，用于指示如何确定 schema。对于"Schema Access Strategy,"（“架构访问策略”），我们将再次使用"Use 'Schema Name' Property,"（“使用'架构名称'属性”），这是默认设置。还要注意，"Schema Name"属性的默认值使用表达式语言来引用名为“ schema.name”的属性。这提供了非常好的灵活性，因为现在我们可以重复使用记录读取器和写入器，并通过使用UpdateAttribute处理器指定模式名称来指定schema name。无需继续创建Record Readers 和 Writers。我们将“ Schema Registry”属性设置为我们刚刚创建和配置的AvroSchemaRegistry。
    
    因为这是一个Record Writer而不是Record Reader，所以我们还有另一个有趣的属性：“ Schema Write Strategy”。现在我们已经配置了如何确定数据的schema，我们需要告诉编写者如何将该schema传达给数据的下一个使用者。默认选项是将schema的名称添加为属性。我们将接受默认值。但是我们也可以将整个schema编写为FlowFile属性，或者使用一些对与Hortonworks Schema Registry交互有用的策略。
    
    现在，我们已经配置了所有内容，我们可以应用设置并启动JsonRecordSetWriter。现在，我们已完成所有控制器服务的设置并启用了：



     现在是建立流程的乐趣所在！上面的过程可能需要5分钟左右，但是它使布置流程超级容易！对于我们的demo，我们将有一个GetFile处理器将数据带入流程。我们将使用UpdateAttribute添加“ demo-schema”的“ schema.name”属性，因为这是我们在chema Registry中配置的schema的名称：

 

    然后，我们将使用ConvertRecord处理器将数据转换为JSON。最后，我们要使用PutFile将数据写出：



 但是，我们仍然需要配置ConvertRecord处理器。为此，我们需要在“属性”选项卡中配置的就是之前已经配置的 Record Reader 和 Writer：



现在，启动处理器，我们可以看到数据流过我们的系统！

 

    同样，既然我们已经定义了这些readers 、writers 和 schema，我们可以轻松地创建一个JSON Reader和一个Avro Writer。添加额外的处理器来拆分数据，查询和路由数据变得非常简单，因为我们已经完成了“困难”部分。

 


CONCLUSION
    在Apache NiFi的1.2.0版本中，我们引入了一些新的控制器服务和处理器，这将使管理处理面向记录的数据（record-oriented data）的数据流变得更加容易。我完全希望Apache NiFi的下一版本将在此基础上增加几个其他处理器。在这里，我们仅涉及了提供给我们的力量的表面。我们可以进一步研究如何转换和路由数据，拆分数据以及迁移模式。同时，用于读取和写入记录的每个Controller Services以及利用这些服务的大多数新处理器都有相当丰富的文档和示例。要查看该信息，可以右键单击处理器，然后单击“使用情况”，或在配置对话框中单击控制器服务左侧的“使用情况”图标。从那里，单击组件用法中的“其他详细信息...”链接将提供非常重要的文档。例如，JsonTreeReader在其附加详细信息中提供了丰富的知识。
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/qq_27022241/article/details/113994624