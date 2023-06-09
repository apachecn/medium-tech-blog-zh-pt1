# 在 Pinterest,我们开发了快速可靠的 iOS 版本

> 原文：<https://medium.com/pinterest-engineering/en-pinterest-desarrollamos-compilaciones-para-ios-r%C3%A1pidas-y-confiables-744f6de81499?source=collection_archive---------6----------------------->

Rahul Malik | iOS 平台技术主管

这篇文章最初发表在 英语;Read the English version [here](/pinterest-engineering/developing-fast-reliable-ios-builds-at-pinterest-part-one-cb1810407b92)

在 Pinterest,我们专注于帮助人们发现鼓舞人心的想法,从晚餐食谱到时尚和家居用品,以及旅行目的地。为此,创建最佳移动产品至关重要,因为 80%的用户通过移动应用访问 Pinterest。特别是在 iOS 团队中,我们一直在努力以尽可能高效和快速的方式改善这种体验,为我们的团队提供最佳的开发和测试环境是实现这一目标的关键一步。

我们最近探讨了如何优化此流程,并着眼于在本地和持续集成环境中提高 iOS 版本的速度和可靠性。此外,我们开始将我们的应用程序模块化为独立的框架,我们需要一个系统来支持此类迁移。我们分析了各种工具,如 Xcode,Cocoapods,Buck 和 Bazel。我们希望在未来实现更稳定的基础,这对于我们快速迭代和向用户发布新功能至关重要。

![](img/b63ae80950420a1e44df3b7c8b9fa2f9.png)

在比较了 Xcode、Cocoapods、Buck 和 Bazel 之后,我们意识到 Bazel 是实现我们目标的最佳选择:为显著提高性能奠定基础,消除编译环境中的可变性,并逐步采用它。因此,我们现在使用 Bazel 发布了我们所有的 iOS 版本,这带来了以下重要成就:

# 地方发展

编译速度更快:清理编译时间从 4 分 38 秒缩短至 3 分 38 秒,改进了 21%。

本地磁盘缓存允许立即重新编译您之前编译的任何内容(其他分支,提交等)。的。

持续集成环境和本地集成环境是相同的,因此编译问题很容易重现。

更高的自动化:代码生成等任务包含在编译图中。

# 持续整合

每个编译都是一个增量编译:由于 Bazel 是可重复的,因此我们在一年多的时间里都没有进行干净的编译。

编译一次并在任何地方重复使用:在引入远程编译缓存后,编译时间缩短到不到一分钟,甚至缩短到 30 秒,因为我们不需要重新编译在任何其他机器上编译的任何内容。

代码生成时间缩短:编译时间从 10 分 24 秒缩短到 7 分 34 秒,改进了 27%。

更改到达测试人员的时间缩短:测试版构建时间从 14 分 32 秒缩短到 7 分 52 秒,改进了 45%。

更快的测试运行:如果修改后的代码不影响测试,则测试运行是即时的。

更高的编译成功率:使用 Bazel 运行编译任务时,编译成功率从大约 80%提高到 97%-100%。

# 迈向快速、可靠的构建的未来

编译速度对于开发人员来说是一个恒定的瓶颈,因为我们使用编译语言(Objective-C/C++)。但是很难量化编译速度。它包括在不同的环境中进行构建,例如持续集成或本地开发。我们还使用不同的工作流场景,例如干净的编译,增量编译,分支切换,重组,回滚更改等。你无法改进你不衡量的东西,因此提高编译速度需要跟踪不同的场景,以便我们能够识别回归并专注于性能工作。

我们可以通过组合更少的工作或更高效的工作来使构建更快。这可能涉及使用不同的工具、改进并行处理或更新项目体系结构以减少源文件的需要。围绕维护模块化架构和清理未引用或与已完成实验相关的死代码的良好实践将有助于保持或改善构建时间。我们使用不同的工具和内部脚本来识别死代码。对于实验,我们使用自动化来添加 clang 注释,以使与实验相关的方法和常量无效,从而允许编译器警告开发人员实验已经结束,代码应该被删除。开发人员根据需要通过定期运行工具来识别未引用的代码,这些工具检查标题是否包含我们编译的图形,并递归查找没有引用的文件。

我们的编译过程必须快速和可靠。可靠的版本是那些可复制的。可重复构建不仅对于重现错误很重要,而且对于确保我们提交我们开发和测试的应用程序的确切版本也很重要。只有当编译环境(输入和输出)一致时,我们才能做到这一点。

环境的变化会对最终产品产生重大影响并产生可变性。一致的环境可确保应用程序以相同的方式运行,无论它是在开发人员的机器上构建还是通过持续集成构建,并消除了花费时间来弄清楚为什么一个构建在一个环境中成功,而在其他环境中失败。

虽然创意和探索都集中在 iOS 上,但实现快速和可重复的构建是我们共同的目标,并将使我们能够扩展客户工程。

# 挑战

决定专注于改进我们的构建过程是基于它对开发人员生产力的影响。随着我们的团队和产品的发展,我们必须投资于开发人员的能力,以便使用一致和快速的构建系统。

规模:随着我们扩展客户端工程,用于支持开发人员、维持或减少构建时间以及提高可靠性的时间也随之增加。帮助开发人员的工程师数量不一定与开发人员数量成比例增长,并且 Xcode 不包含在性能下降时分析构建的工具。

模块化架构:我们开始从应用程序中重构构成我们平台的核心框架,以改进我们的架构,文档和整体质量。这增加了复杂性,因为它需要一个可以管理依赖关系图的构建系统,这些依赖关系图必须按特定顺序配置和编译。虽然在 Xcode 中实现这一点并非不可能,但由于缺乏表达式配置 API,长期的图形设置和维护将非常困难。

编译不稳定性:在我们的代码库之外,有许多工具是用不同的语言编写的(Ruby,Python,Bash 等)。它们需要特定的版本和工具链,这些版本和工具链必须相同才能创建一致的版本。这些版本可能会导致难以可靠重现的错误。对于开发人员来说,构建在本地获得批准并不罕见,但在持续集成中失败,反之亦然。只有某些机器符合创建发布候选版本的要求。本地状态可能会损坏,因此需要进行干净的编译。这是浪费时间。

任务自动化和代码生成:我们依靠代码生成来创建我们的不可变模型(通过 Plank)和注册基础设施(通过 Thrift)。虽然 Xcode 支持脚本阶段的执行,但它不能引入动态工作流,例如代码生成或常规任务自动化,以便将其作为构建过程的一部分,而是需要手动集成生成的源,这意味着开发人员需要做更多的工作并进行培训。它还需要将生成的工件添加到版本控制中,这会增加我们的存储库大小和 git clone 性能。

共享资源:外部存储库的集成路径从未明确过,并且在历史上会定期从其他存储库中复制资源。我们研究了诸如 git subtree 或 git submodule 之类的选项,但这需要在员工培训和开发人员工作流程方面进行更多投资。这造成了混乱,也浪费了时间。Xcode 不允许声明外部编译依赖关系,因此我们必须依靠外部工具来提供这种集成。

# 解决方案

我们一直在寻找解决方案,使我们能够通过工具和自动化来克服这些挑战,而不是增加开发人员的培训和流程负担,并减少浪费时间。我们主要针对以下方面进行优化:

● 快速迭代:我们的解决方案必须提供功能,以显著改善并保持构建和开发人员的速度,这可能是通过更好的并行性和工具中的高级功能来实现的。

隔离开发:一个一致的环境,允许我们拥有可靠的构建,并最大限度地减少可变性和对开发人员生产力的影响。

Monorepo 开发:所有源代码必须保留在一个存储库中。这最大限度地减少了对应用程序进行修改所需的工作量和上下文更改。

分析、监控和分析:我们需要工具来提供有关构建系统的信息,以便识别问题。我们的解决方案必须使我们能够可视化整个编译过程中执行的操作及其持续时间。假设我们有这个,我们将能够经常跟踪详细的变化。

增量编译:一旦我们编译客户端一次,我们应该能够在所有工作流程中以增量和安全的方式编译。这应该包括分支切换、回滚更改或工作流程的其他阶段。到目前为止,干净的构建是最昂贵的构建,通常在本地状态损坏或开发人员尝试诊断未知的构建问题时执行。

# 面向未来可扩展

随着我们的应用程序变得越来越复杂,我们的需求不断变化,我们必须确保我们的构建系统具有足够的可扩展性,以允许进行更改。但它不应该太具体,以至于阻碍了编译过程中的更动态的自动化。这可能包括能够自动执行任务以整合第三方统计分析,[自定义工具链](/@Pinterest_Engineering/ios-linting-at-pinterest-3108d8764390)和[我们在 Pinterest 开发的内部工具](https://pinterest.github.io/plank/)。

修改构建系统是一个重大的变化,我们不能赌一个不能逐步引入的方法。全有或全无的解决方案可能需要暂停开发或维护持久的分叉,并在开发人员环境和持续集成系统之间进行原子迁移。

*我们正在建造世界上第一个视觉发现引擎。全球有超过 4.75 亿人使用 Pinterest 来梦想,计划和准备他们想要做的事情。*[*加入我们的团队!(T8) (T9)*](https://careers.pinterest.com/careers)