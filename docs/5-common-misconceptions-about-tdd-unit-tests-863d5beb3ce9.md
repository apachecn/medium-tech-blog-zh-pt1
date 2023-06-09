# 关于 TDD 和单元测试的 5 个常见误解

> 原文：<https://medium.com/javascript-scene/5-common-misconceptions-about-tdd-unit-tests-863d5beb3ce9?source=collection_archive---------3----------------------->

![](img/b85ed1dd5e85be747d6ebb16ec482c6e.png)

Confused Dog — Gimcor (CC-BY-NC-2.0)

大多数开发人员似乎同意测试是好的，但是开发人员经常对如何测试有不同意见。在本文中，我将打破一些常见的误解，并希望教你一些关于如何从 TDD(测试驱动开发)和单元测试中获得最大收益的事情。

## 1.TDD 太费时间了。业务团队绝不会同意

这是不进行测试的一个常见借口，它真的会伤害您的开发团队和业务。让我们澄清事实。

> 业务团队根本不关心你使用的开发过程，只要它是有效的。

他们真正关心的是业务指标。TDD 如何影响业务度量？

TDD 可以:

*   提高开发人员的生产力(长期)
*   减少客户放弃
*   增加应用程序的病毒因素(即用户增长)
*   降低客户服务成本

像微软、IBM 和 Springer 这样的公司已经在实际项目中测试了 TDD 的好处，他们发现 TDD 过程是非常有益的。他们的发现是:

> [“四个产品的预发布缺陷密度下降了 40%-90%”](https://link.springer.com/article/10.1007/s10664-008-9062-z)

如果没有测试，甚至在实现后添加测试，更多的 bug 会通过开发阶段，进入生产的每个 bug 不仅会浪费开发人员的时间，还会损害公司的品牌和质量声誉。它在客户支持成本上浪费了大量资源。

修复 bug 会中断软件开发的正常流程，这会导致上下文切换，每个 bug 会花费 20 分钟的时间。在这 20 分钟里，开发人员没有做任何有成效的事情——只是试图重新启动他们的大脑来找出新问题的背景，然后恢复他们在中断之前正在处理的问题的背景。

根据您所关注的研究，TDD 过程会增加 10% — 30%的初始开发成本，但是随着时间的推移，当您将持续的维护和错误修复考虑在内时，TDD 可以提高开发人员的工作效率，减少客户放弃，增加您的应用程序的增长因素，并降低客户服务的成本。

如果你认为业务团队会抵制 TDD，你根本没有用事实来证明这一点。

## 2.在你知道设计之前，你不能写测试，在你实现代码之前，你不能知道设计

让我们现在就澄清这件事。一项又一项的研究得出结论，先编写测试比以后再添加测试更有效。有效多少？ [**生产中的 bug 减少 40–80%**](https://www.computer.org/csdl/mags/so/2007/03/s3024.pdf)效率更高。

如果你认为在编写测试之前，你在实现方面做得很好，我在这里告诉你，从统计上来说，你给了自己一个很大的障碍。TDD 需要纪律，这需要一些时间来学习。

没有开发测试优先的 TDD 规程的开发人员通常在他们知道 API 将会是什么样子之前就投入到代码的实现中。

> 他们甚至在设计函数签名之前就开始实现代码。

*这是 TDD 的对立面。*TDD 的要点在于，它迫使你在开始冲锋陷阵之前心中有一个方向，心中有一个方向会带来更好的设计。

在我编写第一个测试之前，我创建了一个 **RDD** 文档(RDD 代表自述驱动开发)。在开始编写代码之前，我不会尝试以 RDD 的形式来充实系统的整个设计，但我确实决定了，*我正在构建一个执行 x 的模块，它需要一个接受 y 并返回 z 的函数签名*

换句话说，我制作了一个包含**梦想代码**的 RDD 文档，其中举例说明了如何使用一个单元的 API。您将在软件库“入门”和 API 指南中看到的同类示例。

对于我的许多测试用例，我只是简单地复制和粘贴来自 RDD 的例子，这给了我一个简单测试的起点。

例如，数字范围生成器的梦想代码 RDD 可能如下所示:

这给了我实际的和预期的值，我可以将它们复制并粘贴到我的单元测试中。上面的第一种用法可以在下面的测试中重新使用，依此类推:

关于如何编写这样的测试，请参见[“每个单元测试必须回答的五个问题”](/javascript-scene/what-every-unit-test-needs-f6cd34d9836d)。

## 3.您必须在开始编写代码之前编写所有的测试

开发人员很难想象 TDD 工作的原因是因为软件设计是一个迭代的、发现驱动的过程。顺便说一句，建造摩天大楼也是如此。与普遍的看法相反，建筑师在任何工作开始之前都不会设计完整的摩天大楼。摄制组必须出去勘察地貌。他们必须检查将要建地基的地面。他们必须确保地面能够支撑摩天大楼的重量。他们必须探测地下，以发现是否有可能坍塌的洞穴系统，是否有水的问题需要解决等等。

100%预先设计在任何类型的工程中都是一个神话。设计是探索性的。我们尝试一些东西，扔掉它们，尝试不同的东西，直到找到我们喜欢的东西。现在，如果你在编写一行实现代码之前就预先编写了每个测试，这将会阻碍探索过程，但是这并不是成功的 TDD 的工作方式。相反:

1.  编写**一个**测试
2.  看着它失败
3.  实现代码
4.  观看测试通过
5.  重复

## 4.红绿，永远重构？

对于上面的指令列表，一个常见的回答是“你忘记了**重构**！”。不，我没有。TDD 的一个巨大好处是，它可以在你需要的时候帮助你重构*，*但是我要跟你说实话:除非你的代码非常难读，或者你已经对它进行了基准测试，发现它太慢了，**你可能不需要重构。**

> “完美是好的敌人。”~伏尔泰

当然，检查你的代码，看看是否有机会让它变得更好，但是不要为了重构而重构。时间在浪费。继续下一个测试。

## 5.一切都需要单元测试

单元测试最适合于**纯函数**——这些函数:

1.  给定相同的输入，总是返回相同的输出
2.  没有副作用(不改变共享状态、保存数据、与网络对话、在屏幕上画图、登录控制台等)

单元测试并不专门针对纯函数，但是你的代码越少依赖任何共享状态或 I/O 依赖，测试就越容易。你的很多代码不容易进行单元测试。您的许多代码将与网络对话、查询数据库、在屏幕上绘图、捕捉用户输入等等。负责所有这些的代码是不纯的，因此，用单元测试来测试要困难得多。

人们最终嘲笑数据库驱动程序、网络 I/O、用户 I/O 和所有其他种类的东西，以努力遵循您的单元需要被隔离测试的规则。

这里有一个将改变你生活的建议:

> [嘲讽是一种码闻](/javascript-scene/mocking-is-a-code-smell-944a70c90a6a)。

如果你不得不做很多嘲讽来创建一个合适的单元测试， ***也许代码根本不需要单元测试。***

也许功能测试会更适合。试图对 I/O 相关代码使用单元测试[会导致问题](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html)，我估计那些抱怨测试优先很难的人正在落入那个陷阱。

你的代码应该足够模块化，这样很容易在程序的边缘保留 I/O 相关的模块，让应用程序的大部分可以很容易地进行单元测试，但是如果你觉得你只是为了测试而强制模块化，而不是因为它实际上使你的应用程序架构更好，你应该重新考虑你的测试策略。

也就是说，如果你想用功能/e2e 测试来做所有的测试**，那也是有问题的。**

**你最终会得到:**

1.  **不充分的测试覆盖，没有正确地运用你的代码的模块单元，以及…**
2.  **一个紧密结合的整体，随着时间的推移变得难以维护。**

**非常小的项目可以摆脱这两者，但是真正成功的项目往往会从那个阶段中成长起来，并且会从更模块化的架构和更好的测试规程中受益。**

**健康的测试套件会认识到有三种主要类型的软件测试都在发挥作用，你的测试覆盖率会在它们之间建立一个平衡。**

**成员们，看看[【单元测试简介】](https://ericelliottjs.com/2016/05/25/introduction-to-unit-tests/)。通过编写生成器库来学习单元测试。包含大量示例、一个介绍视频和 6 个视频演练。不是会员？**

# **[跟随埃里克·埃利奥特学习 JavaScript】](https://ericelliottjs.com/product/lifetime-access-pass/)**

*****埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他曾为 Adobe Systems******Zumba Fitness*******华尔街日报、*******BBC****和顶级录音师包括****Usher*********

*****他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。*****