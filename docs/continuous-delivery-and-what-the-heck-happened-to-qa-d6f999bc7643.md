# 持续交付和 QA 到底怎么了？

> 原文：<https://medium.com/capital-one-tech/continuous-delivery-and-what-the-heck-happened-to-qa-d6f999bc7643?source=collection_archive---------1----------------------->

![](img/7f204941dfff481d0aa654f939bbc041.png)

连续交付(CD)通常被认为是利益相关者的梦想。无需等待漫长的 QA 和 UAT 周期， ***想到它:造出来:出货。*** 你知道吗？连续交货真的很酷。但这并不像有些人声称的那样是一场混战。它真正需要的是远比传统模式中固有的更多的责任和纪律，最重要的是协作。

我现在正在实施这个过程，这是一个紧张的学习过程。最近，在与 Jenkins，[一起进行自动化部署工作时，我听了一个广播节目](http://www.npr.org/programs/ted-radio-hour/443411154/the-meaning-of-work)，我明白了*为什么* CD 可以工作。

该节目讲述了人们如何投入到工作中，以及装配线和工业(工厂)时代的许多方面如何优化产量，同时完全剥夺了人们(工人)的权利。在我从事核心开发的日子里，有时我感觉自己就像一个典型系统开发生命周期中的工厂工人。现在，作为一名技术领导者，我有权利，甚至有义务确保我的团队感受到参与和所有权。

> **经典 SDLC**
> 
> “我开发代码，然后把它扔出围栏。QA 审查、回扔或转发。运营部提问。UAT 把它向前送或者扔回来。在传统的开发中，我们有“工业化”的软件生产，很像工厂方法，将制作一支简单的笔变成一个 14 步的过程，每一步由不同的人完成。

在这个经典模型中，开发人员参与了生命周期的各个阶段，但是经常感觉不到超出他们无形的墙——移交给 QA——的承诺。我们如何帮助他们感受到对所有阶段的承诺？

# 为什么我认为 CD 有效

多年来，我一直是敏捷的粉丝。然后 CD 出现了。我以为我知道如何运行程序。我以为我知道如何运送产品。我做到了。我*做*。CD 不会改变可装运性或质量。只是缩短了敏捷周期。CD 提升了我的贡献、承诺和责任。

它是怎么做到的？再看几个问题就能得到答案了。

## **QA 到底怎么了？**

*从前有开发者……*他们写代码。他们把代码复制到了服务器上。如果有问题，他们会在服务器上修复它们。如果他们小心的话，他们会记得把同样的修改复制到他们自己的电脑上。*(后来我们发明了源码控制)*

事实证明，我们打破东西的次数比客户喜欢的次数还要多！于是，就产生了 QA 这个职位。在代码发布给公众之前，负责确保代码确实如开发人员所说的那样的人。

然后，我们决定 UAT 是重要的一步，所以我们补充说。所以现在，一旦 QA 确定代码如*所宣传的那样运行了*，UAT 就检查以确保它如*所预期的那样运行了*。

在某种程度上，我们意识到开发人员并不总是好的管理员。因此，我们确保我们分离了这些角色，并将开发人员推回编写代码。他们做了什么？他们瞎胡闹。他们变得自满。他们开始抛弃代码，希望得到最好的结果，依靠质量保证和 UAT 来解决问题。

快进到现在…如果你一直在关注 CD 的发展，你可能已经注意到传统 QA 工程师的角色在缩小，在许多环境中，甚至根本不存在。发生了什么事？管理层不再关心质量了吗？还是开发商？

如果我们做的是连续交付，情况正好相反。CD 方式试图*重新授予开发商*权利。增加他们对自己工作的所有权——从键入代码行到确保所有工作都按预期进行。

## 我们的工作是什么？

是的，在 CD 下，我们仍然有产品所有者。是的，在某些情况下，QA 仍然是有意义的(尽管角色已经发生了巨大的变化)。但是通过给开发人员权力去 UAT(甚至生产)他们自己的完整性和质量，我们给了他们权力，并且 ***(陈词滥调警告)*** 有权力就有责任。

开发人员逐渐意识到他们需要再次关注。*想再*关心一下。他们对质量的责任一直延伸到前门，顾客。QA 不会发现问题，没有人想成为导致生产中断或百万美元空指针异常的人。

所以我们需要编写更多的测试。更多*类型*的测试。前端*和后端*测试。我们需要我们的同事为我们的工作编写测试，这样他们就能像我们一样理解它。*共享所有权=团队精神*(我们希望！).

在 CD 下，我们还需要执行主动的流内代码审查(而不是系统完成后的代码审查)。我们需要与产品团队更紧密地互动，一个功能接一个功能，而不是一个冲刺接一个冲刺。同行评审在这个系统中变得更加重要，与传统模式相比，协作水平急剧上升。这就是为什么 CD 会起作用。也等于交叉训练，挺好的。

我们还需要成为运营部，或者与运营部紧密合作，提供建议和促进。我们需要设计更智能的系统，以便减少总体运营工作(集中配置、服务发现、部署自动化)——最终，为此——我们会觉得我们*做了*一些事情。这是关键。这就是诀窍。

> 在一天结束的时候，我们的工作就开始了，那是重新解放，隐藏的承诺，在持续传递给未启蒙的看似“疯狂”中。

## **是的，这就是我们所做的！**

现在，我喜欢看一周内部署数量的报告。不是 0.02，而是 3，或者 5，甚至更多。谈论*“我们什么时候可以发货”*很有趣，是几天还是一周，而不是几个月或更长时间。前面提到的自满并不是懒惰——而是认为我今天可能做的事情并不那么重要——我明天可能会更加努力地工作，或者在 QA 提交错误时解决一些错误。

在生命的长河中，我们必须有目标，有目的，六个月，一年，五年的目标都可以。但是当我们把它们一次又一次地分解，再一次又一次地分解成一个时间表，上面写着*“在两周内，我们将完成这个六个月目标的百分之十！”保持良好的步调可能很难。另一方面，我们可以带着一两天的目标进入周末，然后全力以赴地去实现它们——通常是实现它们。让我们把这种哲学，这种在实现小的短期目标时多巴胺释放的人类生理学应用到我们作为工程师的生活中。*

当然，自动化系统、虚拟化等方面的许多进步帮助实现了这一点。但是不要忘记，它之所以能工作，它确实能工作，是因为作为一名开发人员并不意味着成为一名代码猴子，它意味着成为一名工程师，为你的工作感到自豪，因为你在相关的时间内看到了结果， ***它意味着成为一名所有者。*** 我们就是干这个的。

***声明:以上观点为作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2017 首都一。***

***欲了解更多关于 Capital One 的 API、开源、社区活动和开发人员文化，请访问我们的一站式开发人员门户 DevExchange。***[***developer.capitalone.com/***](https://developer.capitalone.com/)