# 裁剪方形裤子

> 原文：<https://medium.com/square-corner-blog/tailoring-pants-for-square-6c47ea2d729d?source=collection_archive---------2----------------------->

## 裤子构建系统发布了 1.0 版本。

*作者写的* [*艾瑞克艾尔斯*](https://medium.com/u/f19a554b194?source=post_page-----6c47ea2d729d--------------------------------) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

本周，Pants project [宣布了开源 Pants 构建系统的 1.0 版本。Pants 的 1.0 版本表明该工具已经准备好被更广泛的采用。](http://www.pantsbuild.org/1.0.html)

Square 是裤子的骄傲贡献者。自 2014 年以来，Square 的开发人员一直在使用和贡献 Pants [来开发我们的 Java 服务。当我们第一次加入这个项目时，我们发现一个需要大量定制和内部知识来安装和操作的工具。如今，该工具拥有简化的安装流程、丰富的文档、清晰的可扩展设计、充满活力的社区和稳定的每周发布历史。](https://corner.squareup.com/2014/09/trying-on-pants.html)

对于裤子，我们得到:

*   从代码库的当前视图进行可靠的、可重复的构建
*   简化的开发工作流程
*   简单的 IDE 设置
*   与第三方工件库的强大集成
*   切换分支时结果一致
*   可与 CI 构建人员和开发人员笔记本电脑共享的分布式构建缓存
*   许多内置工具可以帮助我们分析大型构建图
*   定义代码模块之间细粒度依赖关系的能力
*   一个可扩展的工具，可以随着我们的需求而增长

# 。/pants 编译服务

要理解 Square 为什么使用 Pants 这样的工具，有助于理解我们的软件生命周期。我们使用[单一代码库](http://www.wired.com/2015/09/google-2-billion-lines-codeand-one-place/) (monorepo)的原因和谷歌一样[。](https://www.youtube.com/watch?v=W71BTkUbdqE)

我们从 master HEAD 构建和发布服务。我们的 Java 代码库几乎完全存放在一个由 900 多个项目和 45，000 个源文件组成的[单一 repo](https://events.rainfocus.com/oow15/catalog/oracle.jsp?event=javaone&search=Monorepi&search.event=javaoneEvent) 中。在这种风格的开发中，我们更喜欢使用全局重构和严格的自动化测试来保持所有代码的一致性，而不是在代码库中保持严格的 API 向后兼容性和长的弃用周期。

我们也坚定地承诺使用开源库。我们广泛依赖于通过 Maven 中央存储库发布的工件。当我们升级一个库时，我们可以在一个地方完成，并更新所有服务的代码依赖。

# 。/裤子创意服务::

有了这么大的代码库，将整个 repo 加载到 IDE 中变得不切实际。在 Square，我们主要使用 IntelliJ IDEA 来开发代码。我们用裤子来配置和发射它。对于日常开发来说，最有价值的特性可能就是能够在 IntelliJ 中快速调出回购的任何部分。只需一个命令，Pants 就可以配置 IntelliJ 来编辑模块及其在 repo 中定义的所有依赖项。

使在 IDE 中配置任何项目变得容易意味着开发人员可以轻松地对代码库中的任何项目做出贡献。能够在分支之间轻松切换鼓励了开发人员的合作。现在，在执行代码评审时，可以方便地在本地检查彼此的工作。将变更请求限制在小的、可管理的块中，并在等待代码评审完成时在它们之间切换，会更容易。

# 。/裤子–救命

我们来到 Pants 项目是为了寻找一种工具来帮助解决我们构建环境中的问题。以前，我们使用 Apache Maven 来编译和打包二进制文件。Maven 是一个强大而流行的工具，它采用模块化设计，易于扩展，并提供了第三方工具和库的出色支持。我们在 Maven 上进行了大量投资，包括许多用于运行代码生成的定制插件，以及在我们的[持续集成](https://github.com/square/kochiku/wiki) (CI)构建系统中支持工件的分布式缓存。

使用 Maven 和我们的“从头开始构建一切”策略[使 Maven 模型变得紧张](http://blog.lexspoon.org/2012/12/recursive-maven-considered-harmful.html)。Maven 被设计成支持一次编辑几个模块，同时依赖二进制工件来获得大多数依赖项。为了支持从头构建整个 repo，我们将 repo 中的每个 Maven 模块都设置为快照版本。

以这种方式使用 Maven 是可行的，但也有缺点。对所有依赖模块进行递归编译会产生大量开销。我们有包装器脚本来帮助我们在这种环境下提高效率，比如说只运行代码生成或者只运行测试的子集。尽管如此，开发人员在某些情况下还是会遇到麻烦，经常不得不处理陈旧的二进制工件和磁盘上的源代码之间的不一致。例如，在使用 mvn install 之后，从 repo 中引入新的变更或者切换回一个旧的分支可能会使它们针对陈旧的代码进行编译。当开发人员经常质疑他们开发环境的完整性时，他们会浪费大量时间清理和重建代码库。

# 。/裤子测试服务:测试

我们的首要任务是允许开发人员在 IDE 中快速配置他们的工作空间。接下来，我们迁移到使用 Pants 作为工具，在 CI 构建器中测试和部署工件。在撰写本文时，我们已经用 Pants 替换了我们在这个回购中使用的所有 Maven，包括:

*   在开发人员工作站和笔记本电脑上开发
*   在我们的持续集成环境中编译和测试代码
*   将工件发布到 Maven 风格的存储库中
*   与 findbugs 和 kloc 等第三方工具集成

取代我们对 Maven 的所有使用并不容易。我们能够通过使用 Maven pom.xml 文件作为事实来源来生成 Pants 配置来做到这一点。在过渡阶段，我们支持这两种工具。通过与 Pants 开源社区的合作，我们能够通过数百个开源贡献来修改 Pants。

# 。/pants 暂存-构建-部署

Pants 开箱即可编辑、编译、测试和打包代码。除此之外，我们还能够利用 Pants 基于可扩展模块的系统。目前最受欢迎的是一个小型定制任务，通过我们的[内部部署系统](https://github.com/square/p2)直接部署到暂存环境中。在这一过程中，其他定制模块运行定制代码生成器，为我们的安全基础设施收集关于构建过程的元信息，并将注释处理器的输出打包到我们的部署系统的 yaml 文件中。今天，我们有大约 24 个内部 Pants 插件来做所有这些事情，加上额外的工具来审计我们的代码库，与我们的 CI 系统集成，并定制我们的 IDE 支持。

# 装运它！

在 Square，Pants 帮助我们实现了 monorepo 风格的大规模开发所承诺的好处。确保开发过程的一致性和可靠性可以提高我们的开发速度。能够快速、可靠地编辑、编译和测试允许开发人员专注于审查和编写代码，而不是纠结于配置工具。我们相信 Pants 已经为更广泛的采用做好了准备，并鼓励你尝试一下。

[](/@ericzundel) [## 埃里克·艾尔斯-简介

### 嗨 Omari，我看了你的帖子，只是想表达我的支持。我觉得你不需要为此负责，或者…

medium.com](/@ericzundel)