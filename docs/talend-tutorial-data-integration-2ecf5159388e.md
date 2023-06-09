# Talend 教程——未来数据集成初学者指南

> 原文：<https://medium.com/edureka/talend-tutorial-data-integration-2ecf5159388e?source=collection_archive---------0----------------------->

![](img/febcce724961e23a5bdff917bd3bef15.png)

Talend Tutorial — Edureka

在当今数据驱动的世界中，各种组织、机器和小工具都会产生大量数据，无论其大小如何。比如你的手机，每次你浏览网页，都会产生一定量的数据。你知道一架商用飞机每小时可以产生高达 500GB 的数据吗？希望现在你能想象这个数据有多大！这就是它被称为大数据的原因。但是所有这些数据几乎都是无用的，除非您对其执行 ETL 操作！相信我，这当然不是一件容易的事。此外，当今业务的实时和快节奏特性，增加了对能够快速和容易地集成系统的工具的需求。好吧，这就是塔伦德来救援的地方。通过这篇关于 Talend 教程的博客，我将解释 Talend 如何帮助构建、测试、部署、调度和监控这些数据。

但在我继续之前，让我列出我今天要讨论的主题:

*   Talend 是什么？
*   Talend Open Studio 简介
*   TOS 安装
*   TOS GUI
*   Talend 作业
*   t 端部部件和连接器
*   [计]元数据
*   上下文变量
*   在塔伦德的第一份工作

# Talend 是什么？

Talend 是一个开源软件集成平台/供应商，提供数据集成和数据管理解决方案。这家公司为大数据、云存储、数据集成、数据管理、主数据管理、数据质量、数据准备和企业应用提供各种集成软件和服务。其总部位于加利福尼亚州雷德伍德城。

以下是 Talend 的一些主要特性:

![](img/0ce13a7e7d6a1249fe34cde29683f3b6.png)

它被认为是云和大数据集成软件的下一代领导者。它提供的软件通过提高数据的可访问性、改善数据质量并将其快速转移到实时决策所需的位置，来帮助公司实现数据驱动。你可以将 Talend 视为这个数据驱动世界的关键基础设施。这是一种开源方法，通过提供强大的软件解决方案，打破了传统的专有模式。它能够灵活地满足所有组织的需求。作为开源软件，它得到了一个庞大的开发者社区的支持。Talend 在 GNU 公共许可证或 Apache 许可证下发布其核心模块的代码。从这里开始，社区中的开发人员可以对产品进行修改和改进，从而使其他 Talend 用户受益。

Talend 提供的各种产品有:

![](img/00ce4cb90ed1f5f553d0f2ce1af3b47b.png)

在上述所有产品中，Talend Open Studio (TOS)是最主要和最常用的。在这个 Talend 教程博客中，我将解释如何使用 Talend Open Studio 进行数据集成。

# Talend Open Studio (TOS)简介— Talend 教程

Talend Open Studio 是一个基于 Eclipse RCP 的开源项目。它支持面向 ETL 的实现，通常用于内部部署。它广泛用于操作系统、ETL 过程和数据迁移之间的集成。talend Open Studio for Data Integration 的设计能够轻松地组合、转换和更新组织中不同位置的数据。它充当代码生成器，生成数据转换脚本和 Java 底层程序。它提供了一个交互式的、用户友好的 GUI，允许您访问包含在 Talend 中执行的每个过程的定义和配置的元数据存储库。下面是 Talend Open Studio 的基本架构。

![](img/9aa00a89b63e580b43a98d84f55a014d.png)

现在让我们尝试在 CentOS 上下载并安装 Talend Open Studio。

# TOS 安装— Talend 教程

**第一步:**转到:【https://www.talend.com/download.】T2

![](img/1f16133d4bf9ab5a488c673319d01125.png)

**第二步:**点击“下载免费工具”。

![](img/9ea62f8ddc2cdb6590f82da62030b3f6.png)

**第 3 步:**再次点击“下载免费工具”获取 zip 文件。

**第四步:**现在解压 zip 文件。

![](img/4112bf7ce44f2c24189a15e1a50f516c.png)

**第五步:**现在进入解压后的文件夹，双击**TOS _ DI-Linux-GTK-x86 _ 64**文件。

![](img/cfd6210dd007f3ae7e7c00d2716c0f87.png)

**步骤 6:** 让安装完成。

![](img/b777db353ddcd613d813b805c44dc423.png)

**第 7 步:**点击“创建新项目”并为您的项目指定一个有意义的名称。

![](img/d080f25d049bee0e923687c9cd1eef4d.png)

**第 8 步:**点击“完成”进入打开的 Studio GUI。

**第 9 步:**右键单击*欢迎选项卡*并选择“关闭”。

![](img/94c7cb8c4daeea2a6b7f136c70d21bff.png)

第十步:现在你应该可以看到 TOS 主页了。

![](img/f3fa5e0dcbd849761ff49d37b9fc9586.png)

# TOS GUI

现在，您已经下载并安装了 Talend Open Studio，让我向您展示一下它的 GUI。Talend Open Studio 由四大部分组成，如下图所示。

![](img/51a1f7011363049abbee293014552172.png)

## 1.贮藏室ˌ仓库

存储库收集了 Talend 中所有可用于描述业务模型或设计工作的技术项目，并以树形结构显示出来。从*库*中，你可以访问各种商业模型、工作设计、可重用程序、文档以及数据库连接。换句话说，*存储库*充当项目中任何工作设计或业务建模所需的所有元素的中央存储。

## 2.设计窗口

该窗口还包括以下部分:

![](img/256bf3697e6956df06ca9fe443405f54.png)

1.  ***工作空间:*** 在这里你可以制定你的工作设计以及商业模式。
2.  ***设计器标签:*** 当您创建一个以图形模式显示作业的作业时，该标签默认打开。
3.  ***代码标签:*** 该标签帮助您可视化代码并突出显示可能的语言错误。

## 3.调色板

组件面板停靠在设计工作区的顶部，以帮助您绘制符合工作流需求的模型。根据您的工作或业务模型，您可以将各种技术组件或形状拖放到设计工作区中。有超过 800 种*元件*可供您选择。

## 4.配置选项卡

配置选项卡位于设计窗口的下半部分。TOS 中有各种配置选项卡。每个选项卡都打开一个视图，显示工作区中当前元素的属性。最常用的配置选项卡有:

![](img/2256fab45756618b641326a1fb189dad.png)

## 1.作业选项卡:

“作业”选项卡提供关于设计器窗口中当前作业的各种信息，包括名称、版本、创建日期和时间等。

## 2.上下文选项卡

“上下文”选项卡用于设置上下文变量以及将使用它们的不同上下文。

## 3.组件选项卡

组件选项卡显示配置组件所需的所有参数。基本上，它收集与设计工作区中选定的图形元素相关的所有信息。

## 4.运行选项卡

“运行”选项卡显示作业的执行进度。此处显示的日志包括任何开始、结束和错误消息。

在这里，你可能会问“什么是工作”，因为到目前为止，我已经多次使用这个术语。所以，在深入之前，让我先给你简单介绍一下一份兼职工作。

# Talend 作业

Talend 中的“工作”基本上是将客户要求转化为技术流程。从技术上讲，它是使用 Talend 构建的任何流程的基本可执行单元。正如您已经知道的，TOS 在后端将所有东西转换成 Java 代码。对于作业，每个作业都被转换成一个 Java 类。让我告诉你如何在 Talend 中创建一份工作。

## 步骤:

右键单击存储库中的“作业设计”并选择“创建作业”。

![](img/92f224f3a19852f74f316ae63767d245.png)

为您的作业指定一个有意义的名称以及目的和描述，然后单击“完成”。

![](img/4e48bd3c12daf92144dffc90928b2d62.png)

完成创建作业后，您将可以访问调色板中的组件。现在，您可以从组件面板中拖动任何您需要的组件，并将其放到工作区中。

![](img/365df1838901158f760b7ef34160b521.png)

但是，为了将组件添加到作业中，首先，您需要知道组件到底是什么，如何一起使用多个组件并将它们连接起来。因此，在本 Talend 教程的下一部分，我将向您介绍 Talend 中可用的各种组件和连接器。

# t 端部部件和连接器

先说组件。

组件是用于在 Talend 中执行单一操作的功能块。在调色板上，你能看到的都是组件的图形表示。您可以通过简单的拖放来使用它们。在后端，组件是作为作业的一部分生成的 Java 代码片段(基本上是 Java 类)。这些 Java 代码会在保存作业时自动编译。根据要求，Talend 作业可能包括一个或多个组件。这里你需要知道的一件事是 Talend 提供了 800 多种组件供你选择。为了便于访问，所有这些组件都被归纳为几个组或族。在这个 Talend 教程博客中，我将向您介绍每个家族中一些最重要和最常用的组件。

## 数据库

该系列提供了 Talend 组件，涵盖了各种需求，如打开连接、读写表、提交事务、为错误处理执行回滚等。Talend 支持超过 40 个 RDBMS，其中一些是 MySQL，MS SQL Server，Hive，Amazon，Azure 等。以下是一些主要使用的 MySQL 组件:

*   ***tMysqlConnection*** :该组件为当前事务打开一个到数据库的新连接。
*   ***tMysqlInput:*** 该组件读取数据库并根据查询提取字段。
*   ***tmysql output:***该组件写入、更新、更改或取消数据库中的条目。
*   ***tMysqlClose:*** 该组件关闭连接数据库中提交的事务。

## 文件

这个系列将各种组件组合在一起，这些组件在所有类型的文件中读取和写入数据，如定界文件、位置文件、XML 文件、Excel 文件等。此外，它还提供了许多组件，帮助执行各种任务，如解压缩，删除，复制，比较等。这个系列又进一步分为子系列，如输入、输出和管理。该系列中几个主要使用的组件是:

*   ***tFileInputDelimited:***这个组件逐行读取一个给定的文件，文件中的字段用一些指定的字符分开。
*   ***tfileinputexel:***这个组件读取一个 Excel 文件(。xls 或者。xlsx)并逐行提取数据。
*   ***tFileOutputXML:***该组件将数据输出到 XML 类型的文件中。
*   ***tFileList:*** 该组件基于文件掩码模式检索一组文件或文件夹，并对它们进行迭代。
*   ***tFileArchive:***该组件根据定义的参数压缩一个或多个文件，并将创建的档案放在选择的目录下。

## 互联网

这个系列包括所有帮助从互联网访问信息的组件，通过各种方式，如 Web 服务、RSS 流、SCP、MOM、电子邮件、FTP 等。该系列中几个主要使用的组件是:

*   ***tFTPGet:*** 该组件通过 FTP 连接帮助检索指定的文件。
*   ***tftput:***该组件通过 FTP 连接复制选中的文件。
*   ***tHttpRequest:*** 该组件向服务器端发送 HTTP 请求，并接收服务器端相应的响应。
*   ***tSendMail:*** 该组件用于向定义好的收件人发送邮件和附件。

## 日志和错误

该系列将所有专用于捕获日志信息和处理作业错误的组件组合在一起。以下是该系列主要使用的组件:

*   ***tLogRow:*** 该组件允许您将*行*数据写入作业日志文件，或写入控制台窗口。
*   ***tlogowcatcher:***该组件收集日志数据并封装后传递给定义的输出。
*   ***tWarn:*** 该组件触发一个警告，该警告经常被 **tLogCatcher** 组件捕获，以获取详尽的日志。
*   ***tDie:*** 该组件向 **tLogCatcher** 发送消息，并允许作业终止作业，并带有指定的退出代码

## 混杂的

这个系列收集了不同的杂项组件，涵盖了各种需求，如创建虚拟数据行集、缓冲数据、加载上下文变量等。这个家族的几个重要组成部分是:

*   ***tMsgBox:*** 这个组件打开一个对话框，里面有一个可点击的 OK 按钮。
*   ***trow generator:***该组件用于使用从列表中取出的随机值生成所需数量的行和字段。

## 管弦乐编曲

这一系列包括各种组件，帮助排序或编排任务和处理作业或子作业等。该系列中主要使用的部件有:

*   ***tLoop:*** 该组件有助于根据指定迭代次数的循环自动执行任务或作业。
*   ***tPrejob:*** 该组件帮助触发执行作业所需的任务。
*   ***tPostjob:*** 该组件帮助触发一个任务执行后所需的任务。
*   ***tSleep:*** 该组件有助于在作业执行中实现休假。

现在，您已经了解了组件，让我们快速了解一下在作业中将这些组件连接在一起的连接器或链接。

Talend 提供各种类型的连接来实现组件之间的通信:

## 排

行连接处理实际的数据流。以下是 Talend 支持的行连接类型:

*   主要的
*   检查
*   过滤器
*   拒绝
*   错误拒绝
*   输出
*   唯一/重复
*   多输入/输出

## 重复

Iterate 连接用于对目录中包含的文件、文件中包含的行或数据库条目执行循环。与其他类型的连接不同，这个迭代链接的名称是只读的。

## 引发

触发器连接用于创建作业或子作业之间的依赖关系，这些作业或子作业根据触发器的性质一个接一个地被触发。触发器连接归纳为两类:

***子作业触发***

*   OnSubjobOK
*   OnSubjobError
*   如果运行

***组件触发器***

*   OnComponentOK
*   OnComponentError 错误
*   如果运行

## 环

Link 连接只能用于 ELT 组件。它用于将表模式信息传输到 ELT mapper 组件，以便在特定的 DB 查询语句中使用。

# [计]元数据

![](img/d4ad0476a857056b0250ef611592f735.png)

Talend 中的元数据是定义性数据，它基本上提供了关于所有在 Talend Studio 中管理的其他数据的信息
。您可以在 TOS 的存储库区域找到元数据。在存储库元数据中，您可以存储关于您可能使用的各种数据源的元数据。这在开发任何项目时都很方便，因为您可以在以后的工作中使用这些数据源，只需将对象从存储库中拖放到工作区中。

在存储库中，您可以存储各种数据源的元数据，如分隔文件、位置文件、XML 文件、数据库、FTP、Azure、Salesforce 等。

# 上下文变量

上下文变量是 Talend 使用的用户定义的参数，在运行时传递到作业中。随着工作从开发升级到测试和生产环境，这些变量可能会改变它们的值。因此，一旦为每个环境正确设置了这些变量，您就可以在这些环境中轻松地执行作业。上下文变量的另一个用途是定义项目中常用的值。您可以通过三种方式创建上下文变量:

## 嵌入式上下文变量

这些上下文变量嵌入到作业中，并像作业设计器下面的*上下文选项卡*中的任何其他组件参数一样进行配置。

## 存储库上下文变量

这些是在多个作业中使用或需要上下文变量时创建的。它们在存储库中集中维护，允许一般访问。

## 外部环境变量

外部上下文变量是那些保存在外部文件中并在运行时加载到 Studio 作业中的上下文变量。

现在，我想你已经准备好设计你在 Talend 的第一份工作了。

在本文的下一部分，我将向您逐步演示一个简单的 Talend 作业，您可以轻松地执行它。

# 在塔伦德的第一份工作

下面是一个演示，首先你将建立一个与数据库的连接，从两个不同的外部 excel 文件读取数据，合并它们，然后将其插入到数据库表中。然后在新的 excel 文件中写入新的表格内容。最后，传输完成后关闭连接。

让我们看看如何一步一步地执行它:

**步骤 1:** 在这个演示中，我使用外部上下文文件来获取数据库细节。为此，首先，您需要创建一个包含所有必要数据库细节的上下文文件。

![](img/a7aa8b8a3ca21ed276f1a08694b40918.png)

**第二步:**创建一份新工作。转到“上下文”选项卡，添加以下详细信息:

![](img/e1e0d13fe9761075c1a174587e80cb3b.png)

**第三步:**现在，在工作区中添加一个“PreJob”和一个“tMysqlConnection”组件，并将它们链接在一起，如下所示。这将在实际作业执行之前建立与数据库的连接。然后转到“tMysqlConnection”组件的“组件”选项卡，添加必要的详细信息:

![](img/440f37f0e420c6d65ae17f44836766c5.png)

**步骤 4:** 在工作区中添加两个‘tFileInputExcel’文件和一个‘tMap’组件，并如图所示链接它们。

![](img/56189ed396092d2c2e8fb103b8a4f62a.png)

**步骤 5:** 现在转到“存储库”并展开“元数据”部分。右键单击“文件 Excel”并选择“创建文件 Excel ”,然后提供必要的详细信息，如下所示。完成后，单击“下一步”。

![](img/7b3aa320fb4f5278df5f23b8437a5d7f.png)

**第 6 步:**提供源文件路径并点击“下一步”。

![](img/f91397d449fdc6af33c36b1872d27b34.png)

**步骤 7:** 勾选“标题”跳过标题行(如果适用)。点击“下一步”。

![](img/942a7c2fea4b52144720b7a839566d66.png)

**第 8 步:**最后为“模式”提供一个名称，并单击“完成”。

![](img/81052efb7d2617063a434e7f2c87f704.png)

**步骤 9:** 转到“tFileInputExcel”组件的“组件”选项卡。选择“属性类型”作为“存储库”，并选择您刚刚创建的元数据。

![](img/c3d7708585538976c575702ca90ba94c.png)

**步骤 10:** 对另一个输入文件重复同样的步骤。

**步骤 11:** 双击“tMap”组件，映射输入和输出表，如下所示:

![](img/497a1f889a15d77d8961bad89ce8533c.png)

**第 12 步:**添加“tMysqlOutput”和“tFileOutputExcel”组件，并链接它们，如下所示:

![](img/e51279ede53a800f6efa29892570776e.png)

**第 13 步:**转到“tMysqlOutput”的组件选项卡，输入如下所示的详细信息:

![](img/4af3af15613861a580f50a8d2584e753.png)

**步骤 14:** 转到“tFileOutputExcel”的组件选项卡，并提供如下所示的详细信息:

![](img/cd9b0134fc8ad1ce0de4b385db58c5fb.png)

**第 15 步:**最后为了完成作业，添加一个“Postjob”和一个“tMysqlClose”组件，如图所示。

![](img/3afc2f3725a1a86600136df7257a715b.png)

**步骤 16:** 转到“tMysqlClose”组件的“组件”选项卡，选择需要关闭的连接。

![](img/39bf3a44a5209beed898badfd8df6961.png)

**第 17 步:**现在转到“运行”选项卡，执行作业。

![](img/39725db7c4f0e92848bc6145460c8b40.png)

所以，这就把我们带到了 Talend 教程博客的结尾。我尽最大努力保持概念简洁明了。希望它能帮助你理解 Talend 和它的各种特性。关于演示，如果你需要练习的数据集，你需要做的就是发表评论。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=talend-tutorial-data-integration)

请留意本系列中的其他文章，它们将解释 Talend 的各个方面。

> 1.[什么是 Talend？](/edureka/what-is-talend-tool-881b41fc1f5f)
> 
> 2. [Talend ETL 教程](/edureka/talend-etl-tool-d08b497c33f)
> 
> 3. [Talend 大数据教程](/edureka/talend-big-data-tutorial-7500d3b457a8)
> 
> 4. [Talend 架构](/edureka/talend-architecture-18dd64db2408)

*原载于 2017 年 12 月 19 日*[*www.edureka.co*](https://www.edureka.co/blog/talend-tutorial-data-integration/)*。*