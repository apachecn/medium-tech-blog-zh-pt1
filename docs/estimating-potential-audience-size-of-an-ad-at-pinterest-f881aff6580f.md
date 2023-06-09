# 在 Pinterest 上估算广告的潜在受众规模

> 原文：<https://medium.com/pinterest-engineering/estimating-potential-audience-size-of-an-ad-at-pinterest-f881aff6580f?source=collection_archive---------1----------------------->

[Chanheum (Sean) Cho](https://www.linkedin.com/in/seanccho/) | ML 工程师，Ads 情报；[瑞新强](https://www.linkedin.com/in/ruixin-qiang-16443aaa/) |ML 工程师，Ads 情报；[kesh ava Subramanya](https://www.linkedin.com/in/keshavasubramanya/)|工程经理，Ads 智能

![](img/0eb6a09a374d952903bc8076d9b9957d.png)

# 介绍

对于广告商来说，了解广告的潜在受众的规模是一个重要的考虑因素。它使广告商能够估计可能对他们广告的产品或服务感兴趣的总人口，并提前计划他们的预算。Pinterest 的广告智能团队在广告管理器中提供了一项名为潜在受众规模的服务，因此广告商可以在配置广告组时了解他们的目标受众规模。该服务随着受众目标的更新而实时更新评估。

![](img/1471208896a95c697709b3314337e1e7.png)

在本文中，我们将概述潜在受众规模特性是如何工作的，然后详细研究每一部分。现在让我们开始吧！

# 它是如何制作的

总体而言，潜在受众规模由三个不同部分组成:对广告请求进行抽样以构建索引，汇总索引中与 Ads Manager 中的目标规格相匹配的广告请求数量，以及推断估计值以获得潜在的每月受众规模。这些步骤相当于图 2 所示的广告服务过程的反向操作。在广告派发中，将多个广告组作为每个广告请求的输入，以检索获胜的广告组，而在受众规模确定中，将广告请求中的用户属性，例如他们的人口统计或兴趣，作为输入，以检索匹配的广告请求的数量。

![](img/a3b22b262e6e1db5fc4b52f582df8c89.png)

像广告服务基础设施一样，潜在受众规模利用了一个名为 Muse 的内部搜索引擎。

# 创建目标用户索引

为了搜索具有匹配目标标准(如人口统计或兴趣)的用户，我们需要建立用户及其信息的搜索索引。这允许我们快速检索具有特定目标规范的用户，因此我们可以在 Ads Manager UI 上提供实时更新。

创建索引从提取历史广告请求日志开始，构建称为广告查询的中间对象，在请求中保存用户上下文。这允许我们通过解析广告查询在特定用户和他们的目标信息之间进行映射。此信息用于计算潜在受众规模，不会对外显示。

存储在广告查询中的可映射数据被翻译成 Muse 可以理解的文档。这些文档由用户 id、人口统计和兴趣等关键字组成，如图 3 所示。这些文档在转换成搜索索引后，使我们能够查询任何字段来检索匹配的用户 id。这些文档被输入到 Muse 的索引构建管道中，为潜在受众规模的下游规划服务提供动力，这将在下一节中介绍。

![](img/263eee04bf4bf64cb764eebc7a0cf8a2.png)

倒排索引是每天用我们前一天收集的新数据构建的。这是因为目标规范中的一些用户特征，如用户兴趣，来自 Pinterest 的机器学习模型输出。当模型更新改变其预测时，搜索检索将受到影响，这导致我们的估计发生变化。例如，如果一个模型对用户兴趣的预测从“露营”变为“露营”和“旅行”，那么我们现在有比以前更多的用户与“旅行”相关联。每日更新索引使我们能够管理这种系统级更新，并为广告客户提供最新的估计。

# 规划服务—请求

潜在受众规模的第二个组成部分是规划服务。如图 4 所示，该服务负责将 Ads 管理器中的目标规范转换为对后端的请求，以便计算潜在受众规模并适当地返回。

![](img/486c1e6131110230070a8118fbdd9810.png)

在 UI 下面，广告管理器将*计划请求*发送到后端服务。*计划请求*中的目标规范被翻译成 Muse 理解的查询，名为 *squery* ，其中每个目标类别都被相应地连接起来。例如，在逻辑析取中选择多个年龄组，其中年龄组和语言结合在一起，如图 5 所示。

![](img/cef19a7b420b8f2b749a7208368ae9c4.png)

在目标规范之上，广告商可以创建一个名为 [*受众*](https://help.pinterest.com/en/business/article/audience-targeting) 列表的用户名单。深入解释*受众*列表超出了本文的范围，因此我们不会进一步解释，它们可以被认为是广告商定义的用户列表。这里重要的考虑是他们有很多，因为每个广告商创建他们自己的列表，每个*观众*列表包含许多用户 id。

在潜在受众规模中，我们为每个*受众*列表利用一个布隆过滤器来跟踪与*受众*列表相关联的用户 id 以及他们的用户总数。这使我们能够有效地管理内存和存储的使用。

规划服务是一种多层次的服务，其中将广告商指定的目标规范翻译成*查询*只是其中的一部分。规划服务由四个不同的层组成，其中每一层都有不同的逻辑来将请求传递给下层，将响应传递给上层。这些层如图 6 所示。

![](img/26ad711fabd6c28fb9a443f17e48adf2.png)

在从广告管理器向 Muse 发送请求的上下文中，重写和检索层执行重要的任务。重写层修改*计划请求*中的关键字和兴趣，例如词干关键字、提取派生兴趣以及根据内部引用将兴趣转换成它们的规范形式。最后，检索层将更新后的请求翻译成*查询*，并将搜索请求提交给 Muse 集群。

# 规划服务—响应

规划服务的另一面，也是潜在受众规模的最后一部分，是处理来自 Muse 集群的响应。简而言之，这是从 Muse 到 *sanitize* 层，如图 6 所示。来自 *sanitize* 层的更新响应最终显示在广告管理器中。

检索层接收来自 Muse 集群的响应，其中包括聚合的用户数。如果在目标中配置了一个或多个*受众*列表，检索层将根据*受众*列表的 bloom 过滤器的误报率进行调整，如图 7 所示。

![](img/4c7d477d4e2246d3bd79a0588176172f.png)

根据估计的用户数来估计下限和上限。这个范围有助于我们更恰当地表达我们对估计值的信心。

一旦检索层返回估计的受众规模范围，在预测层和*净化*层执行进一步的调整。如果广告商在广告管理器中指定了日期范围，预测层将每日用户计数范围外推至每月或任何给定的日期范围。作为最后一步， *sanitize* 层应用额外的业务逻辑来进一步细化我们的评估。

一旦评估完成， *sanitize* 层将最终的用户规模评估发送回广告管理器，广告管理器向广告商显示潜在的受众规模。

# 结尾部分

在这篇文章中，我们回顾了我们如何为广告商提供潜在的受众规模估计，以便他们能够提前计划一个成功的广告活动。它包括我们如何建立一个搜索索引来根据他们的特征找到用户，以及我们如何创建规划服务，其中*规划请求*通过多层修改来检索匹配目标规范的用户。我们还研究了如何调整 Muse 的响应，以将每日用户估计值外推至每月用户估计值，同时考虑到*受众*列表的使用情况。

Pinterest 的广告智能团队一直在寻找能够帮助广告商高效、轻松地实现目标的功能。潜在观众规模服务只是这些特征之一。如果你对我们建立的其他类似功能感兴趣，看看[广告商推荐系统](/pinterest-engineering/advertiser-recommendation-systems-at-pinterest-ccb255fbde20)或[活动预算](/pinterest-engineering/campaign-budgets-at-pinterest-be94f15a4527)。如果你对通过提供超能力来帮助 Pinterest 上的企业繁荣感到兴奋，来[加入我们的团队](https://www.pinterestcareers.com/job/12630410/machine-learning-engineer-ads-intelligence-palo-alto-ca/)！

# 致谢:

我们要感谢以下跨职能团队(非详尽列表)的贡献-

**Ads 情报团队:**弗拉维奥·博索兰、Chanheum (Sean) Cho、崔天元、于浩、达尼洛·努内斯·多斯桑托斯、佩雷·奥贡沃勒、芮新强、施、梅拉妮·斯塔姆、凯沙瓦·萨勃拉曼尼亚、

**广告客户解决方案:** Kelvin Jiang、
**广告定位团队:** Jacob Gao、Paul、Scott Zou

**广告人体验团队:** Argun Alparslan，Dani Gnibus，Robbie Holmes，Frannie Huang，，Leo Lam，Priyanka Patil，Meera Srinivasan

*要在 Pinterest 上了解更多关于工程的知识，请查看我们的* [*工程博客*](https://medium.com/pinterest-engineering) *，并访问我们的*[*Pinterest Labs*](https://www.pinterestlabs.com/?utm_source=medium&utm_medium=blog-article-link&utm_campaign=cho-qiang-subramanya-june-6-2022)*网站。要查看和申请空缺职位，请访问我们的* [*职业*](https://www.pinterestcareers.com/?utm_source=medium&utm_medium=blog-article-link&utm_campaign=cho-qiang-subramanya-june-6-2022) *页面*