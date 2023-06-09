# 中简化和优化管道处理。网

> 原文：<https://medium.com/compendium/simplifying-parallell-processing-in-net-9d873730f7eb?source=collection_archive---------1----------------------->

![](img/9f054373b2fb746e8df6d7e4721decde.png)

[https://pixabay.com/images/id-5146458/](https://pixabay.com/images/id-5146458/)

我目前在为一家公司做一个项目，该公司负责向挪威首都奥斯陆提供区域供暖。作为其中的一部分，我们每天都会收到物联网传感器数据。我们的一个应用程序负责从 Azure EventHub 读取测量值，处理测量值，然后将结果写入 Google Bigtable 和 Google Pub/Sub。该应用程序运行在 Kubernetes 集群中，能够根据 CPU 的使用情况进行水平伸缩。问题是，如果应用程序正在进行大量的 IO 操作，CPU 使用率可能会很低，这使得根据 CPU 负载设置自动伸缩变得很困难。为了加快测量的处理速度，我们可能需要扩展到 10-20 个实例，因为可用资源的利用率很低。这种增加很可能导致更高的成本，因为每个运行的容器也会增加一些资源开销。这就是为什么我们作为开发人员需要善于编写应用程序，以最佳方式利用可用的 CPU、内存和 IO。不仅仅是为了降低成本，还为了减少完成工作所需的时间，最大限度地减少系统延迟，为用户带来快速响应的体验。

在这篇文章中，我们将介绍几种技巧，让你的。净工作量。

想象一下这样一个应用程序，它负责从队列中读取消息，在将结果写入数据库之前进行某种处理。

![](img/33cdfe688751e433a635c22a51f16e22.png)

# 单线程的

如果这个应用程序是以单线程循环的形式编写的，那么这个应用程序的基本循环将类似于下面的例子。

这个应用程序将完成这项工作，但很可能不会利用最多的 CPU，因为有许多 IO 操作正在发生。像这样的单个实例将很难实现高吞吐量。运行多个实例将是增加吞吐量的唯一选择。

通过将其转化为一个简化的图表，可以看到输入、CPU 和输出一次只利用了一个。如果读取、处理和写入各花费相同的时间，像这样的应用程序将只利用大约三分之一的可用资源。

![](img/1232ce53c32259ac949f329ef6eac34a.png)

# 多重任务

可以在应用程序内部运行多个任务来增加吞吐量，只需实例化单线程示例中任务的多个实例。

这可能会增加吞吐量，但不一定。我们可能会很幸运，平均分配输入、CPU 和输出负载。

![](img/ceee4c056893b58f1d006ae038f56d79.png)

但最有可能的是，消息不会像稳定的流一样流入。在这种情况下，我们将结束几个线程同时读取、处理和写入。这导致了峰值负载和大量未利用的资源。

![](img/6382ba8c0336ad8832c991d04a2cdc3d.png)

运行多个任务也带来了一系列新的挑战，因为现在我们必须确保一切都是线程安全的。这通常包括访问共享集合和依赖项的不同锁定方法，这会增加复杂性，并且通常会影响整体性能。

# 穿线。频道

下一个选项是一个相当新的选项，于 2019 年底推出。它为. NET 中的内部排队提供了一种线程安全的方法。但是它要求我们重新思考我们的简单示例，并使用[线程将它分成三个不同的阶段。线程间交接的通道](https://docs.microsoft.com/en-us/dotnet/api/system.threading.channels?view=net-5.0)。

现在，应用程序被重新编写以允许关注点的分离。这使得读、处理和写任务尽可能快地工作，每个任务一个任务。这给了我们更多的优化空间，但也有一些陷阱要注意。允许任务全速运行既有好处，也可能是一个问题，因为处理任务很可能是这里的瓶颈。让我们从为管道中的处理步骤添加更多任务开始。

我们现在有四个任务运行处理步骤。这也假设第 27 行的`ProcessMessageAsync`是线程安全的。但是管道的另外两部分，第 10 行上的`ReadMessagesAsync`和第 40 行上的`WriteResultAsync`，不一定是线程安全的。值得指出的是，线程。Channel 只能帮助在线程之间转移对象的“所有权”,但它确实很好地完成了这一任务，允许我们围绕这一点来设计代码。

但是，如果四个处理任务还不够，并且应用程序从输入队列中读取消息的速度比应用程序处理消息的速度快得多，该怎么办呢？随着通道存储越来越多的消息，这将导致应用程序内存被填满。这是因为我们使用了[通道。创建**Un**bounded<T>而不是](https://docs.microsoft.com/en-us/dotnet/api/system.threading.channels.channel.createunbounded?view=net-5.0#System_Threading_Channels_Channel_CreateUnbounded__1)[通道。create bounded<T>T](https://docs.microsoft.com/en-us/dotnet/api/system.threading.channels.channel.createbounded?view=net-5.0#System_Threading_Channels_Channel_CreateBounded__1_System_Int32_)。使用后者会限制通道中的可用空间，设置通道中可用的最大消息数。

我们现在引入了反压力的概念，当没有更多空间容纳信息时，迫使阅读任务等待。

通过将代码分成三部分，我们在将结果写入数据库时也引入了一个新问题。更具体地说，我们现在一次只写一个结果，这会导致对数据库的大量请求。为了优化对数据库的写入，我们必须成批处理结果，例如一次 200 个结果。

曾经简单的应用程序，现在变得复杂得多。但是反过来，应用程序现在能够更好地利用可用的 CPU 和 IO。

最后一个我们在使用踩踏时没有解决的问题。用于多线程的通道，正在冲洗管道。如果我们通过调用取消令牌上的`Cancel()`来请求应用程序关闭，所有的管道任务将会停止它们正在做的事情，而不会清空通道。这当然也可以处理，但是我们必须自己实现逻辑。

# 任务并行库

我们将介绍最后一个解决方案:TPL ( [任务并行库](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/dataflow-task-parallel-library))。)这个库已经存在很多年了，是我简化多线程的首选库。TPL 可以被认为是线程的超集。通道，这为管道处理增加了许多功能。注意，对于基本的东西，基本通道比 TPL 快得多，比如将对象从一个线程传递到另一个线程。但是您必须自己实现和维护大量从 TPL 免费获得的逻辑。

让我们看看这里发生了什么。首先，我们创建一个 [TransformBlock](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.dataflow.transformblock-2?view=net-5.0) 块，通过将输入从`Message`转换为`Result`来处理消息。`BoundedCapacity`设置为 1000，对阅读器施加反压力。`MaxDegreeOfParallelism`设置为 4，允许它使用最多四个并行任务。此后，使用 [BatchBlock](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.dataflow.batchblock-1?view=net-5.0) 创建 200 个结果的批次，然后将每个批次移交给最后的 [ActionBlock](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.dataflow.actionblock-1?view=net-5.0) ，将结果写入数据库。

通过将这些模块连接在一起，我们实现了一个带背压的管道。通过将链接块设置为`PropagateCompletion`，我们可以通知管道的入口块不再提供消息。这将导致管道在最后一条消息流经每个块时关闭，从而有效地为缓存的消息和结果刷新管道。

通过使用 TPL，我们能够实现更简单的代码，而不必自己实现所有的东西。到目前为止，任务并行库是一个被低估的，而且对许多人来说，是一个未知的库，它可以简化高性能应用程序的编写。

请注意，尽管我们关注的是如何最好地利用每个应用程序实例的 CPU 使用率，但这也可以很容易地写成从输入队列读取或写入数据库的多线程。什么最适合你取决于你系统的瓶颈是什么。

如果您的应用程序是 IO 绑定的，这意味着您的应用程序被一个子系统的 IO 所限制，如果您不断地达到那个 IO 限制，那么您能做的就不多了。如果您的应用程序没有持续达到 IO 限制，或者线程。渠道或第三方物流方法可能会帮助您挤出最后一点性能。

如果您的应用程序不受 IO 限制，并且您使用的是像 Google Bigtable 或 Azure CosmosDb 这样的分布式数据库，那么您仍然会受到每个实例使用 *x* 个 CPU 的限制。即使穿线。Channel 和 TPL 将帮助你平均分配负载，它们使用起来仍然比仅仅启动几个任务来利用更多的 CPU 更复杂。

*   如果您的应用程序不需要在线程之间传输对象，并且主要做 CPU 密集型工作，那么多任务可能是最简单的选择。
*   如果您的应用程序需要在线程间传递对象，但不在乎通道未被刷新时丢失消息，那么请使用[线程。频道](https://docs.microsoft.com/en-us/dotnet/api/system.threading.channels?view=net-5.0)可能是你的选择。
*   但是如果您的应用程序由具有许多步骤的长管道组成，或者需要控制管道的清洗，您应该考虑使用[任务并行库](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/dataflow-task-parallel-library)。