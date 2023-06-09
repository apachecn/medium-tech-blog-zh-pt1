# 偏见、公平和可解释性——构建负责任人工智能的步骤

> 原文：<https://medium.com/walmartglobaltech/bias-fairness-and-explainability-steps-towards-building-responsible-ai-dc735b06279?source=collection_archive---------1----------------------->

在过去的几十年里，人工智能(AI)已经成为现代工业的游戏规则改变者。据预测，到 2030 年，AI 可能为全球经济贡献 15.7 万亿美元[1]。人工智能存在于我们佩戴的智能设备、我们与之互动的家庭助理以及我们驾驶的汽车中——基本上，人工智能现在无处不在，几乎涵盖了我们生活的所有方面。然而，“*拥有强大的力量就意味着巨大的责任*，因此最重要的是，控制我们生活的人工智能不受任何*不希望的*偏见的影响，也就是说，它的判断(预测)应该是*公平的*，如果我们对它的预测不满意，我们应该能够理解为什么人工智能会做出这样的预测——所有这些都是构建“负责任的人工智能”所必需的值得注意的是，“负责任的人工智能”可能还包括法规遵从性、隐私和安全性等。这里没有涉及到。
在本文中，我们将向读者介绍人工智能中的偏见、公平和可解释性等基本概念，并借助例子区分这些相关概念。稍后，我们将深入研究公平，并探索各种工具来实现人工智能中的公平。

我们先来看看:

# **什么是偏见？(是好是坏？)**

尽管“bias”一词在普通英语中出现时通常具有负面含义，但在机器学习(ML)的上下文中不一定如此。事实上，Tom Mitchell 提倡有偏见，以便为 ML 模型学习适当的概括[2]。例如，具有先前的领域知识(即，偏见)有助于设计合理的约束，以确定探索哪个解决方案空间和修剪哪个。偏差(或统计术语中的先验知识)可用于 ML 中，以对数据的实际知识、学习到的概括的预期用途甚至关于训练数据的知识进行建模。

然而，在本文的其余部分，我们将使用术语“偏差”来表示“不期望的偏差”，当 ML 模型由于在训练过程中做出错误的假设而对某些特征值显示出*系统性偏见*时，就会出现这种偏差。

下面列出了一些偏差来源:

1.  **样本偏差:**当一个群体在训练数据集中代表过多或代表不足时，就会出现样本偏差。
2.  **标签偏差:**在创建训练数据的过程中，当注释过程引入偏差时，会出现标签偏差。
3.  **结果代理偏差:**当我们使用代理指标而不是实际(理想)指标时，就会出现这种偏差。
4.  **确认偏差:**是处理(或解释)信息的倾向，确认一个人现有的信念。
5.  **群体归因偏差:**当训练数据对某些群体不对称时就会出现。

接下来，我们来调查一下:

# **什么是公平？**

在维基百科中提到:“*在机器学习中，一个给定的算法被称为* ***公平*** *，或者具有* ***公平*** *，如果它的结果独立于给定的变量，特别是那些被认为敏感的变量，比如不应该与结果相关的个体特征(即性别、种族、性取向、残疾等)。).*

让我们通过一个例子来更好地理解什么是公平。

**问题陈述:**根据性别(男，女)和信用评分(评分越高越好)，从下面给出的 12 名申请人中选择 6 名申请人进行贷款审批。

注意，在这个问题中，我们将性别视为敏感属性。

![](img/fef34e3a29b08e0e624eedb3d190bfe0.png)

**解决方案 1(无群体意识):**第一种解决方案可以是简单地根据申请人的信用分数对其进行排序，并选择前 6 名，而完全不考虑申请人的性别。这在公平文献中被称为“群体无意识”,因为在做出决定时没有注意到申请人属于哪个群体(男性或女性)。

注意，按照这个解决方案，所有贷款被批准的申请人(显示在绿色方框中)碰巧都是男性。

![](img/167e1e964b6c6fe7871b914a6854b9ba.png)

“群体无意识”的解决方案最初可能看起来很公平，因为它似乎支持精英管理。但是，如果女性申请人的信用评分较低，因为在我们的社会中，与男性相比，女性传统上得不到充分的服务，例如，报酬过低，那该怎么办呢？以下解决方案试图平衡现有的社会差异。

**解决方案 2(组阈值):**这种方法试图为不同的组提出合适的阈值，以在组间的有利预测(在这种情况下，贷款被授予)中实现平衡。比方说，在这个例子中，我们将男性的信用评分阈值设为 500，女性设为 300，那么结果如下所示。

请注意，根据该解决方案，5 名男性申请人和只有 1 名女性申请人获得贷款批准。

![](img/10923347b3b20a4cd26d52660d3e359e.png)

有些人可能认为解决方案 2 不够“公平”，因为 62.5%(8 人中有 5 人)的男性候选人获得了贷款，而只有 25%(4 人中有 1 人)的女性候选人获得了贷款。这就引出了下面的解决方案。

**解决方案 3(人口统计均等):**该策略规定，对于一个敏感属性(性别)，每个群体(男性、女性)的有利预测比例应该相等。因此，根据这种方法，结果如下。

请注意，根据该解决方案，4 名男性(即 50%为男性)和 2 名女性(即 50%为女性)的贷款获得批准。

![](img/2f8925a45daf5654bcd3ff0fe91ec127.png)

因此，重要的是要注意到*公平性是主观的*，也就是说，应该采用哪种解决方案取决于政策制定者。

此外，公平性有时会导致机器学习模型的准确性降低。

值得注意的是，到目前为止解释的公平指标是“群体公平”的例子，我们在其中探索少数群体是否没有得到充分的服务。个人公平的概念是在[7]中引入的，它基本上是说相似的个人应该得到相同的服务。

# **如何实现公平？**

显而易见，我们想到的实现公平的第一个解决方案是从数据集中删除敏感属性，例如年龄、性别、种族。然而，由于数据 *x* 和属性 *a* 之间存在的*潜在偏见* (LP)，事情并不那么简单。可以使用 *x* 和 *a* 之间的*互信息*进行测量，如下式所示:

![](img/cfa42c56ed581a4757a8acae2ffd0d38.png)

例如，假设种族是一个敏感的属性，因此，我们从训练数据中移除该信息；然而，一个人的种族可能仍然来源于他/她的姓氏。

这就引出了*偏差缓解算法*，该算法可用于消除敏感属性引入的偏差。根据应用的阶段，这些算法可以分为三组。这里我们列出了这三种算法的优缺点；我们强烈要求读者去查阅[4]以了解更多的细节。

1.  **预处理算法** —应用于训练数据
    优点:
    可能是最佳的，因为偏差通常存在于数据中
    一旦实现了无偏表示，就可以用于其他训练(迁移学习)
    缺点:
    数据可能以复杂的方式产生偏差，因此预处理算法可能不起作用
    它可能被法律禁止，因为有时对预处理数据进行审计会变得困难
2.  **处理中算法** —在模型训练期间应用于模型
    优点:
    准确性和公平性指标可以得到更好的控制
    缺点:
    必须重新设计训练方法
3.  **后处理算法** —应用于预测标签
    Pro:
    训练后的模型可视为黑盒
    Con:
    很难消除数据中固有的偏差并保持准确性

# **偏见与公平**

如前所述，公平是主观的，通常在受影响的群体涉及人类时定义。所以，所有的偏见未必都会导致不公平。准确的说，*一个公平的算法不应该偏向敏感属性*。

为了说明，让我们考虑有一个机器学习模型被训练来执行光学字符识别(OCR ),它偏向于正常字体而不是书法字体；例如，它善于识别左边用“Times New Roman”字体写的单词实际上是“Hello”，但当同一个单词用“Apple Chancery”字体写时就不那么好了，如右图所示。

![](img/ab9e87274afb8e4489f34ec4aeb69395.png)

这种偏向某些特定字体的做法，尽管不可取，但可能不会被视为对政策制定者不公平。然而，现在让我们考虑应用相同的模型从下面两幅图像中识别品牌名称。

![](img/ddc85c599de41e1747ffc7ddbafca6d4.png)

我们的模型更有可能正确预测品牌名称“PETSI”而不是“Coke-Cola ”,因为前者是常规字体，而后者是书法字体。当然，使用这种有偏见的模式可能会导致一个品牌比另一个品牌受到更有利的待遇，即可能会对业务和服务产生直接影响，这可能是政策制定者所不能容忍的，这时偏见可能会超越为不公平。

# 人工智能中的公平与沃尔玛相关吗？

这是一个值得思考的重要问题。我们坚信这与*有关*。沃尔玛的行为对人类生活有直接影响——无论是顾客、卖家还是员工，许多这样的行为都是由以人工智能为核心的系统采取的。

让我们首先考虑一个与人力资源流程相关的例子。对于一个行业来说，招聘可能是在其领域内成长和脱颖而出的最关键的一步。通常有许多申请人的简历被人工智能列入候选名单，这样的选择不应该偏向一个人的种族、性别等。，从而坚持我们的“包容”政策

这是另一个关于搜索和推荐的例子。最受欢迎的品牌经常被给予最高排名——尽管这看起来似乎很公平，但这种行为阻碍了不太受欢迎的品牌，他们可能会选择转移到其他平台；此外，对于客户来说，增加推荐列表的多样性有助于了解他们的个人偏好。

# **什么是可解释性？**

可解释性(也称为可解释性)是解释机器学习模型做出的预测的能力。换句话说，可解释性是机器学习的一个分支，它处理破译为什么一个模型做出一个特定的决定，并以人类可以理解的方式表示它。

有各种各样为可解释性开发的方法，根据这些方法是否需要模型可用，这些方法可以分为以下两组。

1.  **模型不可知**(也称为**黑盒**):这些方法只需要模型的输入和输出来解释它的行为。
2.  **特定于模型的**(也称为**白盒**或**玻璃盒**):这些方法还需要访问模型来解释它的行为。

或者，基于这些方法所提供的可解释性的级别，可解释性的方法也可以分为以下两类。

1.  **全局可解释性**:这些方法试图解释整个模型，因此基于对整个模型如何运行的理解来解释单个预测。
2.  局部可解释性:这些方法试图在不理解整个模型的情况下单独解释单个预测。

我们建议读者仔细阅读[5],以便更深入地理解可解释性。

# **公平性与可解释性**

可解释性比公平有更大的范围，因为它适用于任何偏见。

此外，我们也许能够根据某种度量标准得出一个模型是否公平的结论，而无需解释为什么会这样(并由此发展出如何修正它的见解)。为了理解这种说法，让我们重新审视一下上面这个根据一个人的信用评分和性别来审批贷款的问题。假设政策制定者已经决定采用的公平标准应该是*人口均等*，即应该选择相同比例的男性和女性候选人。现在，考虑我们训练的模型做出以下预测。

![](img/5ec28d7de77824436e05ba6731e407a0.png)

显然，这种模式未能实现公平，因为 62.5%的男性申请人获得了贷款，而只有 25%的女性申请人获得了贷款。然而，我们不确定模型为什么做出这些预测；这就是可解释性方法派上用场的地方。在可解释性方法的帮助下，特别是对比解释[6]，人们可能会得出结论，当前的决定是基于男性信用分数为 500 分、女性信用分数为 300 分的阈值做出的，为了实现人口统计上的均等，阈值应该调整为男性 550 分、女性 200 分。

我们希望这篇博客能帮助你理解人工智能中的偏见、公平和可解释性的基本概念，并区分它们。
接下来，我们基于不同的相关指标，比较了三种不同的与检测和改善人工智能公平性相关的开源工具——公平性指标、AIF360 和 Fairlearn。
请注意，所有这些工具目前正由各自的行业进行改进，因此以下评估(截至 2021 年 1 月)可能也需要在未来进行改进。

![](img/9c25f52e133c4e5cd4399633c472614b.png)

# **作者:**

1.  [Kunal Banerjee](https://kunalbanerjee.github.io/) ，沃尔玛全球技术部数据科学基金会员工数据科学家
2.  [Vijay agneswaran](https://www.linkedin.com/in/vijaysrinivasagneeswaran/)，沃尔玛全球技术部数据科学基金会总监

# **参考文献:**

[1] [衡量奖励——人工智能对你的企业有什么真正的价值，你如何才能利用它？](https://www.pwc.com/gx/en/issues/analytics/assets/pwc-ai-analysis-sizing-the-prize-report.pdf)

[2]汤姆·米切尔。学习归纳中对偏差的需求。

[3] [性别薪酬差距的状况。](https://www.payscale.com/data/gender-pay-gap)

4 Trisha Mahoney，Kush R. Varshney 和 Michael Hind。 [AI 公平性:如何度量和减少机器学习中不想要的偏差](https://krvarshney.github.io/pubs/MahoneyVH2020.pdf)，2020。

[5]克里斯托弗·莫尔纳尔。[可解释机器学习](https://christophm.github.io/interpretable-ml-book/)，2020。

[6]提姆·米勒，[对比解释:一种结构模型方法](https://arxiv.org/abs/1811.03163)，2018。

[7]辛西娅·德沃克、莫里茨·哈特、托尼安·皮塔西、奥梅尔·莱因戈尔德和里奇·泽梅尔。[通过意识实现公平](https://arxiv.org/abs/1104.3913)，2011 年。