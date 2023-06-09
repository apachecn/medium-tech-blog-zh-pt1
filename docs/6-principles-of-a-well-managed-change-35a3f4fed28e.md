# 良好管理变革的 6 项原则

> 原文：<https://medium.com/capital-one-tech/6-principles-of-a-well-managed-change-35a3f4fed28e?source=collection_archive---------7----------------------->

## 变化是好的，但前提是管理得当

![](img/2db92b54309f74bb38984ec6ab7f29af.png)

> 我们经常听说变化是好的，但是[一些数据表明](https://landing.google.com/sre/sre-book/chapters/introduction/)多达 70%的停机是由于实时系统的变化造成的。

作为工程师，我们都忙于左改右改以增强客户体验，但在最激烈的时刻，我们可能会以负面的方式中断业务。这可能是由于遗漏了一个关键需求，遗漏了对外部组件的依赖，或者仅仅是未能充分测试预期的更改。我们有时会忘记遵循简单的准则，甚至是我们都认为对交付卓越的客户体验非常重要的最基本的准则。所以我想写下一些原则，可以帮助我们作为工程师在生产中引入管理良好的变化。

**那么*良好管理变革的原则是什么？***

请注意，每项原则都与您在引入生产变革之前需要自问的一个问题相关联。

# 谁需要了解这一变化？

这可能是要问的最重要的问题，这也是为什么它是第一个。回答这个问题将有助于你确定你在这次变革中的利益相关者。可能的利益攸关方包括:

*   业务/产品团队
*   客户
*   后端
*   领导力
*   合规/风险/网络

知道谁是你的利益相关者意味着你知道和谁交流。提前沟通你的变化是非常重要的，以避免意外。此外，开放和清晰的沟通渠道给了你的利益相关者一个机会，如果他们看到风险就阻止你[不要去]，或者从验证前/验证后的角度为变化做准备[去]。如果涉众拥有一个依赖于你的变更的变更，这变得极其重要，反之亦然。

# 你在改变什么？

它是一个版本中的一个变更还是多个变更？这些变化是否相互依赖，各自的风险是什么？是业务变更(增强/新功能)、缺陷修复、非功能性变更(优化/调整)、合规性相关变更(如 AMI/RDS、安全风险补救等基础设施升级)吗？)?此变更是否会违反贵公司的任何合规性要求，如赛博、SOX 合规性等。

做改变的时候，要避免把太多鸡蛋放在同一个篮子里！这意味着**尽可能地解耦不相关的变更**，并确保业务变更尽可能地与其他变更类型相隔离。这将防止一个坏的变化影响所有其他好的变化，并允许您取得渐进的进展。

# 你应该在什么时候进行这种改变？

假设你正在网上购买一张 800 美元的机票。您已经填写了所有信息，并到达点击购买按钮的最后一页。除了航空公司进行了生产变更，导致了一个错误，迫使你从头开始。现在那张票的成本变成了 900 美元。更糟糕的是，这是最后一个房间，现在已经被别人预订了。

你会生那家航空公司的气，对吗？这是一个不合时宜的生产变化的简单例子，更糟糕的事情可能会发生。*(请注意，我自己也经历过这个，超级抓狂。)*

时机就是一切，回答这个问题很重要。始终假设最坏的情况是可能的，并考虑如果您的变更对顾客/客户造成影响，可能会出现什么问题。理想情况下，**最好在没有生产流量的时候执行变更。**但是，对于 24*7 应用程序，您需要了解流量模式，并确定生产变更活动最少的最安全窗口。我知道每个人都喜欢白天发布。相信我，我也是。但这对你的客户来说真的是最好的吗？

# 这种变化将如何部署？

这可能是一个价值百万美元的问题。没有一个正确或错误的答案，它总是与相关性有关，并取决于我们今天讨论的所有其他问题。生产系统由许多组件组成。我们有代码本身、配置、基础设施等。它们一起构成了一个生产堆栈。

现在，我将简单介绍一下在过去 12 年的 it 工作中，对我和我的团队最有效的部署方法。

*   **把你的产品栈当作代码，并对其进行版本化。**切勿修改正在提供流量的现有堆栈。相反，将流量从旧堆栈切换到新堆栈。如果可行，转换应该是渐进的。换句话说，您应该使用蓝绿色方法来部署堆栈，但流量翻转可以通过金丝雀或蓝绿色部署，具体取决于使用情形。
*   **稳扎稳打**。您的更改是向后兼容的吗(没有接口更改，没有新的端点，没有依赖于此的客户端更改，没有对后端更改的依赖)？如果是，那么最好遵循缓慢而稳定的方法，以增量的方式交换流量。在我的上一个组织中，我们过去常常将流量从 0 切换到 5、25、50 到 100%。对于大多数版本，我们最多切换 5%的流量，观察 24 小时，然后在第二天晚上逐渐增加到 100%。这让我们能够观察客户/顾客对变化的反应。
*   **全有或全无的方法。**非向后兼容的变更不能通过缓慢而稳定的方法进行。您可以将流量从 0 翻转到 100，但您需要有可靠的监控/警报，以便在需要时将其翻转回来。

关于这种部署方法，需要注意以下几点:

*   我试图通过将不同的组件(比如 API)放在不同的堆栈中来分离它们。我会为四个 API 选择四个较小的堆栈，而不是将它们放在一个大堆栈中。围绕这种方法还有一些争论的空间，但是我发现从操作的角度来看，它给了我完全的灵活性。
*   我通常会将部署的各个阶段分开，以便可以在白天(如果需要的话)进行准备，而在工作时间之外进行生产切换。
*   我避免在蓝绿色部署中使用两个区域。区域是为了冗余，应该总是相同的。因此，两者上的转移也应该并行发生。但是，如果有一个有效的用例，您可以将区域用于渐进切换。

# 如何以及何时宣布成功？

宣布成功的唯一方法是监控实际流量，并确保一切都在控制之下(错误率、延迟等)。).在看到完整的生产流量之前，您不应该仅仅依赖内部验证，也不应该认为变更是成功的。这意味着需要强大的监控/警报，并有一个团队随时待命，以防需要完全回滚。这也意味着在上次变更后的 24-48 小时内，您不应计划任何新的变更。我见过发布后 2-3 天的回滚。这并不罕见。

最重要的是你的利益相关者应该对这种改变感到高兴。在宣布成功之前，一定要得到利益相关者的认可。

# 如果失败，您如何撤销这一更改？

这是前面原理的延伸。会有这样的一天，当你基于内部/外部的签署而宣布成功，并在几个小时/几天(有时是几周)后意识到一个用例/涉众受到了负面影响。如果您决定进行一次完整的回滚——假设在导致问题的变更之后没有新的版本出现——您将需要让您的旧堆栈重新运行。

如果你有一个固定的发布节奏，一个关于回滚窗口的协议(如果需要的话),并且你不删除回滚窗口内的旧栈，这对每个人都是最好的。如上所述，如果使用蓝绿色部署选项，回滚将非常容易。

# 最后的想法

以完全负责和尊重的态度对待生产环境，这是您对客户的责任。如果你认为你不能承担这个责任，不要要求进入生产。想象一下，你坐在飞机出口附近——你为飞机上的每个人承担了额外的责任。如果达不到那个期望，最好还是坐到别的地方去。如果你真的接受了这个责任，在你做出改变之前，考虑你自己是否被授权，并运用你最好的判断力。记住——变化是好的，但前提是管理得当。

这些是作者的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2019 首都一。