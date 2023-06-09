# Pinterest 上的 API 评测

> 原文：<https://medium.com/pinterest-engineering/api-profiling-at-pinterest-6fa9333b4961?source=collection_archive---------0----------------------->

夏嫣·穆克吉|美国石油学会实习生

当我在实习的第一天走进 Pinterest，得知我将专注于分析 API 网关服务——Pinterest 产品的核心后端服务——时，我唯一的想法是“什么是*分析*？”。侧写经常作为次要项目或较低优先级的问题被搁置一旁，而且它通常不在大学计算机科学课程中讲授。本质上，编写服务是第一位的，对它们进行概要分析是第二位的(如果真的发生的话)。此外，概要分析并不总是被视为优化的先导，这会导致浪费时间优化不会显著影响生产性能的代码。也就是说，为了创建一个真正高性能的系统，概要分析是软件开发过程中的一个关键步骤。

在我到达 Pinterest 之前，已经为我们所有的节点和 Python 服务构建了一个基本的 webapp，伴随着定期调度的 CPU 分析作业(以及随之而来的 [flamegraph](http://www.brendangregg.com/flamegraphs.html) 生成)。我今年夏天的主要目标是扩展这个工具来支持我们的 API 网关服务，同时使它在将来可以灵活地用于其他服务。最终目标是用它来分析所有 Pinterest 服务。我工作的三个功能是**内存分析**，端点**运行成本计算**和**死代码检测**。

# 为提高优化而求解

我主要从事优化工作，包括扩展资源跟踪和分析工具。就生产中的性能而言，我们对 API 网关服务的资源利用率的评估仅限于 CPU 使用率。需要对 API 网关服务的哪些部分是高性能的，以及代码库的哪些部分需要质量改进进行整体评估。有了这些信息，开发人员的资源可以被分配到性能最差的端点，我们就可以改进整个优化过程。

# 侧写到底是什么？

软件概要分析是一种类型的 [*动态编程分析*](https://en.wikipedia.org/wiki/Dynamic_program_analysis) ，旨在通过收集与软件执行相关的统计数据来促进优化。常见的评测指标包括 CPU、内存和函数调用频率。本质上，分析脚本与另一个正在执行的程序一起执行一段时间(或整个被分析的脚本)，然后它们输出相关统计数据的概要(即摘要)。然后，记录的度量可以用于评估和分析程序的行为。

有两种常见的分析方法:

基于事件的分析:

*   跟踪某些事件的所有发生(例如函数调用、返回和抛出的异常)
*   确定性(更准确)
*   大量开销(较慢，更有可能影响分析流程)
*   示例 Python 包包括:cProfile/profile、pstats、line_profiler

统计分析:

*   通过定期探测调用堆栈来采样数据
*   不确定性(不太准确，尽管您可以通过随机降噪来缓解)
*   低开销(更快，不太可能影响分析流程)
*   示例 Python 包包括:vmprof、tracemalloc、statprof、pyflame

由于开销较低，我们选择对生产机器进行统计分析。如果该作业定期长时间运行，准确性会提高，而不会由于大量开销而增加响应延迟。虽然概要分析很重要，但它不应该损害生产性能。

# 内存分析

## TL；DR: tracemalloc 跟踪内存块

我们的 API 网关服务是用 Python 编写的，所以最明显的解决方案是使用现有的 Python 包来收集内存堆栈跟踪。Python 3 的 [tracemalloc](http://pytracemalloc.readthedocs.io/index.html) 包是最吸引人的，有一个大问题:我们仍然使用 Python2.7。虽然我们的 Python 3 迁移正在进行中，但这个项目还要好几个月才能完成。这种不兼容性迫使我们除了使用反向移植的 pytracemalloc 包之外，还要修补和分发我们自己的 Python 副本。再次提醒您，更新到 Python 的最新版本对于最新工具的性能和利用率都是理想的。

这里的基本方法是在一个远程节点(我们的 API 生产主机之一)上运行一个脚本，该脚本每隔 15 分钟发送一次信号，触发信号处理程序(注册为在某个信号到达时执行的函数)。

信号是一个合适的选择，因为当不运行信号处理程序时，它们不会增加任何开销，并且因为我们不希望在所有机器上一直启用分析。(即使是 0.1%的大规模开销也很昂贵。)我们决定过载 [SIGRTMIN+N 信号](http://man7.org/linux/man-pages/man7/signal.7.html),以便根据接收到的信号启动和停止分析工作。堆栈跟踪被收集并保存到/tmp/中的一个临时文件中。另一个脚本在远程主机上运行，以生成火焰图，然后所有文件都保存到一个持久的数据存储中，并由我们的 Profiler webapp 提供。

# 运营成本计算

## TL；DR:寻找昂贵的端点(以及它们的所有者！)

端点运营成本的计算需要结合两种数据:资源利用率数据和请求指标。我们的资源利用率信息以两种单位提供，即美元和实例小时，并且每月提供一次。

使用请求计数，可以计算每个端点的相对流行度。该流行度被用作划分 API 网关服务所使用的总资源的权重。因为我们的大部分请求数据是以每分钟的请求为单位的，所以我决定将成本也分解到那个时间尺度。因为每个 API 端点都有一个所有者，所以也要计算给定所有者团队的平均运营成本。

![](img/76ea0639e8f546d6c0aa1279f00a47bf.png)

识别最昂贵的端点以及他们所属的工程师/团队的能力，鼓励所有权和对他们的表现的主动监控。需要注意的是，这些计算出来的指标并不是事实的绝对来源；相反，它们的重要性在于它们如何相互比较。识别异常值的能力是主要目标，而不是量化精确的货币影响。

这种方法是幼稚的，因为它没有正确考虑 CPU 时间，也没有区分高成本的处理程序(API 网关中特定于端点的函数)和高成本的请求。例如，请求可能触发不一定属于 API 网关服务的异步任务，具有不同参数的相同端点可能具有不同的成本结构(不同的处理程序也是如此)，并且下游服务处理与给定的 API 请求无关。

我们可以通过创建一个集成测试平台来解决这些缺陷，该平台运行一组已知的类似生产的请求，并测量相对于应用程序基线所花费的 CPU 时间。我们可以通过将它合并到我们的持续集成过程中来进一步最大化它的影响，为开发人员提供对他们的代码更改的影响的关键见解。此外，通过给定的 Request-ID 进行跟踪将使我们的整体架构能够得到更全面的覆盖。

# 死码检测

## TL；DR:发现废弃的代码(并删除它)

未使用和无主的代码是一个问题。旧实验、旧测试、旧文件等。如果它们能够在雷达下飞行的话，可以迅速地扰乱存储库和二进制文件。发现哪些文件的哪些行在生产中*从不*执行既有用又容易操作。为了识别隐藏在我们服务中的死代码，我使用了一个标准的 Python 测试覆盖工具。

虽然测试覆盖工具的主要用途是发现单元和集成测试遗漏了哪些代码行，但是您可以运行一个作业，在随机选择的生产机器上运行相同的工具，以查看“遗漏”了哪些代码行。由于该作业一天要运行几次，所以在给定一天的所有运行中通常会遗漏的行就会出现。显示了该文件的注释版本，以便更容易地可视化哪些行是“死的”,以及应该联系谁来查看是否应该删除代码。

对于开始检测死代码来说，这是一个相当幼稚的实现。正在讨论的代码库可能被多个服务和作业使用，确定它们之间的共同失效代码是一个复杂的问题，仍然需要更加小心地解决。它也相当昂贵，因为它使用基于事件的收集技术，而不是统计采样。

# 下一步是什么

## TL；DR:这都是为了优化

我在“大数据”方面没有太多经验，但在构建了这些工具并开始定期运行这些作业后，我被大量涌入的数据轰炸了。我的直觉反应是把它们都塞进 webapp，让开发者自己找出什么是有用的(*越多越好，对吗？*)。然而，我很快意识到，虽然这些数据对我这个花了数周时间来生成数据的人来说是有意义的，但对于没有使用过火焰图或缺乏运营成本视角的工程师来说，这些数据是不透明的，可以说是难以理解的。就效用而言，简单地传播原始数据远非最佳。

我注意到，我创建的新功能很可能有以下主要用途，因此这些是即将浮出水面的关键见解:

*   查找使用最多内存的文件和函数
*   工程师发现他们的 API 端点有多贵
*   从我们的存储库中清除死代码的起点
*   找到 API 中最流行和最昂贵的部分

为了在全公司推广这个工具，我举办了一个工程范围的研讨会，讨论火焰图读取和其他分析活动。仅在两天内，就发现并实现了两种不同的潜在优化(单行变更)，为公司节省了大量年度开支。

从表面上看，这些用例提供了关于 API 的资源利用以及代码库的哪些部分在生产中使用较少的广泛见解。然而，鸟瞰更令人兴奋和激动。并不是代码库的所有部分都是同等创建的——有些函数的执行次数要比其他函数多得多。在很少执行的端点上花费太多时间是对开发人员资源的不良利用，也是优化性能的最差策略；换句话说，盲目优化并不是真正的优化。