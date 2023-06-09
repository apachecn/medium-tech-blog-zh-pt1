# 掌握 JavaScript 面试:什么是函数式编程？

> 原文：<https://medium.com/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0?source=collection_archive---------0----------------------->

![](img/4320009cfb49000b456e4fc8486ff8fa.png)

Structure Synth — Orihaus (CC BY 2.0)

> “掌握 JavaScript 面试”是一系列的帖子，旨在帮助候选人准备在申请中高级 JavaScript 职位时可能遇到的常见问题。这些是我在真实面试中经常用到的问题。

函数式编程已经成为 JavaScript 世界的热门话题。就在几年前，很少有 JavaScript 程序员知道什么是函数式编程，但是我在过去 3 年中看到的每个大型应用程序代码库都大量使用了函数式编程思想。

**函数式编程**(常缩写为 FP)是通过组合**纯函数**，避免**共享状态、** **可变数据、****副作用**来构建软件的过程。函数式编程是**声明性的**而不是**命令性的**，应用程序状态流经纯函数。与面向对象的编程相反，在面向对象的编程中，应用程序状态通常是共享的，并与对象中的方法放在一起。

函数式编程是一种**编程范式**，这意味着它是一种基于一些基本的、定义的原则(如上所列)来思考软件构造的方式。编程范例的其他例子包括面向对象编程和过程编程。

与命令式或面向对象的代码相比，功能性代码往往更简洁、更可预测、更易于测试——但是如果你不熟悉它以及与它相关的常见模式，功能性代码也可能看起来更密集，并且相关文献对于新手来说可能是难以理解的。

如果你开始谷歌函数式编程术语，你会很快碰到学术术语的砖墙，这对初学者来说是非常可怕的。说它有一个学习曲线是一种严重的保守说法。但是如果你已经用 JavaScript 编程有一段时间了，那么很有可能你已经在你真正的软件中使用了很多函数式编程的概念和工具。

> 不要让所有的新单词把你吓跑了。这比听起来容易多了。

最困难的部分是把所有不熟悉的词汇都记在脑子里。在开始理解函数式编程的含义之前，需要理解上面看似简单的定义中的许多概念:

*   纯函数
*   功能组成
*   避免共享状态
*   避免状态突变
*   避免副作用

换句话说，如果你想知道函数式编程在实践中意味着什么，你必须从理解那些核心概念开始。

一个**纯函数**是这样一个函数:

*   给定相同的输入，总是返回相同的输出，并且
*   没有副作用

纯函数有很多在函数式编程中很重要的属性，包括**引用透明性**(你可以用函数调用的结果值替换它，而不改变程序的含义)。读[“什么是纯函数？”](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)了解更多详情。

**函数组合**是将两个或两个以上的函数组合起来，以产生一个新函数或执行某种计算的过程。比如 composition】(点表示“composed with”)就相当于 JavaScript 中的`f(g(x))`。理解函数组合是理解如何使用函数式编程构建软件的重要一步。阅读[“什么是函数合成？”](/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0)求更多。

# 共享状态

**共享状态**是存在于共享作用域中的任何变量、对象或内存空间，或者作为对象的属性在作用域之间传递。共享范围可以包括全局范围或闭包范围。通常，在面向对象的编程中，通过向其他对象添加属性，对象在范围之间被共享。

例如，一个计算机游戏可能有一个主游戏对象，其中角色和游戏项目被存储为该对象所拥有的属性。函数式编程避免了共享状态，而是依靠不可变的数据结构和纯粹的计算从现有数据中派生出新数据。关于功能软件如何处理应用程序状态的更多细节，参见[“10 个更好 Redux 架构的技巧”](/javascript-scene/10-tips-for-better-redux-architecture-69250425af44)。

共享状态的问题在于，为了理解一个函数的效果，你必须知道该函数使用或影响的每个共享变量的全部历史。

假设您有一个需要保存的用户对象。您的`saveUser()`函数向服务器上的一个 API 发出请求。与此同时，用户用`updateAvatar()`更改他们的个人资料图片，并触发另一个`saveUser()`请求。在保存时，服务器发回一个规范的用户对象，该对象应该替换内存中的内容，以便与服务器上发生的更改同步，或者响应其他 API 调用。

不幸的是，第二个响应是在第一个响应之前收到的，所以当第一个(现在已经过时的)响应被返回时，新的概要文件 pic 会从内存中清除，并被旧的替换。这是一个竞争条件的例子——一个与共享状态相关的非常常见的错误。

与共享状态相关联的另一个常见问题是，改变调用函数的顺序会导致一连串的失败，因为作用于共享状态的函数是依赖于时间的:

Timing dependency example

当您避免共享状态时，函数调用的时间和顺序不会改变调用函数的结果。对于纯函数，给定相同的输入，你将总是得到相同的输出。这使得函数调用完全独立于其他函数调用，这可以从根本上简化更改和重构。一个函数的变化，或者一个函数调用的时间不会波及到程序的其他部分。

在上面的例子中，我们使用`Object.assign()`并传入一个空对象作为第一个参数来复制`x`的属性，而不是在适当的位置对其进行变异。在这种情况下，它相当于简单地从零开始创建一个新对象，没有`Object.assign()`，但是这是 JavaScript 中的一种常见模式，用来创建现有状态的副本，而不是使用突变，我们在第一个例子中演示了这一点。

如果你仔细观察这个例子中的`console.log()`语句，你会注意到我已经提到过的东西:函数组合。回想一下，函数的组成是这样的:`f(g(x))`。在这种情况下，我们将`f()`和`g()`替换为`x1()`和`x2()`组成:`x1 . x2`。

当然，如果你改变了构图的顺序，输出也会改变。操作顺序仍然很重要。`f(g(x))`并不总是等于`g(f(x))`，但是不再重要的是函数外部的变量发生了什么——这是一件大事。对于不纯的函数，除非你知道函数使用或影响的每个变量的全部历史，否则不可能完全理解函数的作用。

消除函数调用时间依赖性，就消除了一整类潜在的错误。

# 不变

一个**不可变的**对象是一个创建后不能被修改的对象。相反，**可变**对象是任何在创建后可以修改的对象。

不变性是函数式编程的一个核心概念，因为没有它，程序中的数据流就会丢失。状态历史被抛弃了，奇怪的错误会悄悄进入你的软件。更多关于永恒的意义，请看[“永恒之道”](/javascript-scene/the-dao-of-immutability-9f91a70c88cd)

在 JavaScript 中，重要的是不要将`const`与不变性混淆。`const`创建一个变量名绑定，创建后不能重新分配。`const`不创建不可变的对象。您不能更改绑定引用的对象，但是您仍然可以更改对象的属性，这意味着用`const`创建的绑定是可变的，而不是不可变的。

不可变对象根本不能改变。通过深度冻结对象，可以使一个值真正不可变。JavaScript 有一个冻结一级对象的方法:

但是冻结的对象只是表面上不可变的。例如，以下对象是可变的:

正如您所看到的，冻结对象的顶层基本属性不能改变，但是任何也是对象的属性(包括数组等)仍然可以变异——所以即使冻结的对象也不是不可变的，除非您遍历整个对象树并冻结每个对象属性。

在许多函数式编程语言中，有特殊的不可变数据结构，称为 **trie 数据结构**(读作“树”)，它们实际上是深度冻结的——这意味着没有属性可以改变，不管属性在对象层次结构中的级别如何。

尝试使用**结构共享**来共享对象的所有部分的引用内存位置，这些部分在对象被操作者复制后没有改变，这样使用的内存更少，并且对于某些类型的操作来说能够显著提高性能。

例如，您可以在对象树的根处使用身份比较来进行比较。如果身份是相同的，您不必遍历整个树来检查差异。

JavaScript 中有几个库利用了尝试，包括 [Immutable.js](https://github.com/facebook/immutable-js) 和 [Mori](https://github.com/swannodette/mori) 。

我对两者都进行了试验，并且倾向于在需要大量不可变状态的大型项目中使用 Immutable.js。如需了解更多信息，请参见[“改善 Redux 架构的 10 个技巧”](/javascript-scene/10-tips-for-better-redux-architecture-69250425af44)。

# 副作用

副作用是除了返回值之外，在被调用函数之外可以观察到的任何应用程序状态变化。副作用包括:

*   修改任何外部变量或对象属性(例如，全局变量或父函数作用域链中的变量)
*   记录到控制台
*   写在屏幕上
*   写入文件
*   向网络写信
*   触发任何外部过程
*   调用任何其他有副作用的函数

在函数式编程中，副作用是最容易避免的，这使得程序的效果更容易理解，也更容易测试。

Haskell 和其他函数式语言经常使用 [**单子**](https://en.wikipedia.org/wiki/Monad_(functional_programming)) 从纯函数中隔离和封装副作用。单子的主题足够深刻，可以写一本书，所以我们把它留到以后再说。

你现在需要知道的是，副作用动作需要与你的软件的其余部分隔离开来。如果您将副作用与程序逻辑的其余部分分开，您的软件将更容易扩展、重构、调试、测试和维护。

这就是为什么大多数前端框架鼓励用户在独立的、松散耦合的模块中管理状态和组件呈现。

# 通过高阶函数实现可重用性

函数式编程倾向于重用一组通用的函数实用程序来处理数据。面向对象编程倾向于将方法和数据放在对象中。这些协同定位的方法只能操作它们被设计操作的数据类型，并且通常只能操作包含在特定对象实例中的数据。

在函数式编程中，任何类型的数据都是公平的。同一个`map()`实用程序可以映射对象、字符串、数字或任何其他数据类型，因为它将一个函数作为适当处理给定数据类型的参数。FP 使用**高阶函数**实现了它的通用效用欺骗。

JavaScript 有**一级函数**，它允许我们将函数视为数据——将它们赋给变量，将它们传递给其他函数，从函数返回它们，等等…

**高阶函数**是将函数作为参数、返回函数或两者兼有的任何函数。高阶函数通常用于:

*   使用回调函数、承诺、单子等抽象或隔离动作、效果或异步流控制…
*   创建可以处理多种数据类型的实用程序
*   将函数部分应用于其参数，或者创建一个定制函数，以便重用或组合函数
*   获取一个函数列表，并返回这些输入函数的组合

## 容器、函子、列表和流

函子是可以被映射的东西。换句话说，这是一个容器，它有一个接口，可以用来将一个函数应用到其中的值。看到函子这个词，应该会想到“可映射”。

前面我们了解到，同一个`map()`实用程序可以作用于多种数据类型。它通过提升映射操作来处理仿函数 API。`map()`使用的重要流量控制操作利用了这个接口。在`Array.prototype.map()`的例子中，容器是一个数组，但是其他数据结构也可以是函子——只要它们提供映射 API。

让我们看看`Array.prototype.map()`如何允许您从映射实用程序中抽象出数据类型，从而使`map()`可用于任何数据类型。我们将创建一个简单的`double()`映射，简单地将任何传入的值乘以 2:

如果我们想在游戏中对目标进行操作以使他们奖励的点数翻倍呢？我们所要做的就是对传递给`map()`的`double()`函数做一个微妙的改变，一切仍然正常:

在函数式编程中，使用函子和高阶函数等抽象概念来使用通用实用函数操作任意数量的不同数据类型是非常重要的。你会看到类似的概念以各种不同的方式应用于。

> "一个随时间表达的列表就是一个流."

你现在需要理解的是，数组和函子并不是容器和容器中的值这一概念适用的唯一方式。例如，一个数组只是一个事物的列表。随着时间的推移而表达的列表是一个流——因此您可以应用相同类型的实用程序来处理传入事件流——当您开始使用 FP 构建真正的软件时，您会经常看到这种情况。

# 陈述性与命令性

函数式编程是一种声明性范式，这意味着程序逻辑是在没有显式描述流控制的情况下表达的。

**命令式**程序用几行代码来描述用来实现预期结果的具体步骤——**流程控制:如何**做事。

**声明性的**程序抽象出流程控制过程，并代之以描述**数据流的代码行:做什么**。*如何被抽象掉。*

例如，这个**命令式**映射接受一个数字数组，并返回一个每个数字乘以 2 的新数组:

Imperative data mapping

这个**声明性的**映射做同样的事情，但是使用功能性的`Array.prototype.map()`实用程序抽象出流控制，这允许您更清楚地表达数据流:

**命令式**代码频繁使用语句。**语句**是一段执行某种动作的代码。常用语句的例子有`for`、`if`、`switch`、`throw`等

**声明式**代码更依赖于表达式。一个**表达式**是一段计算出某个值的代码。表达式通常是函数调用、值和运算符的某种组合，通过计算产生结果值。

这些都是表达的例子:

```
2 * 2
doubleMap([2, 3, 4])
Math.max(4, 3, 2)
```

通常在代码中，你会看到表达式被赋值给一个标识符，从函数中返回，或者传递给一个函数。在赋值、返回或传递之前，首先对表达式进行计算，然后使用结果值。

# 结论

函数式编程有利于:

*   纯函数而不是共享状态和副作用
*   可变数据的不变性
*   强制性流量控制的功能组合
*   许多通用的、可重用的实用程序使用更高阶的函数来处理许多数据类型，而不是只处理其协同定位数据的方法
*   声明性的而不是命令性的代码(做什么，而不是怎么做)
*   语句之上的表达式
*   容器&基于特定多态性的高阶函数

# 家庭作业

学习和实践这一组核心功能阵列附加组件:

*   `.map()`
*   `.filter()`
*   `.reduce()`

使用 map 将下列值数组转换为项目名称数组:

使用过滤器选择点数大于或等于 3 的项目:

使用 reduce 对这些点求和:

## 探索该系列

*   [什么是闭包？](/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36#.ecfskj935)
*   [类和原型继承有什么区别？](/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9#.h96dymht1)
*   [什么是纯函数？](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976#.4256pjcfq)
*   [什么是函数构成？](/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0#.i84zm53fb)
*   [什么是函数式编程？](/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0#.jddz30xy3)
*   [什么是承诺？](/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261#.aa7ubggsy)
*   [软技能](/javascript-scene/master-the-javascript-interview-soft-skills-a8a5fb02c466)

> 这篇帖子被收录在《作曲软件》一书中。 [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一篇*](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976) *|* [*下一篇>*](/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30)

[![](img/649c1c875d8140aa42e9e3d9ffedf8e5.png)](https://ericelliottjs.com/premium-content/lesson-pure-functions)

[Start your free lesson on EricElliottJS.com](https://ericelliottjs.com/premium-content/lesson-pure-functions)

***埃里克·埃利奥特*** *是一位科技产品和平台顾问，著有* [*【作曲软件】*](https://leanpub.com/composingsoftware) *，*[*EricElliottJS.com*](https://ericelliottjs.com)*和*[*devanywhere . io*](https://devanywhere.io)*和 dev 团队导师。他为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报】、*******【ESPN】、*******BBC、*** *和顶级录音艺术家包括***

****他和世界上最美丽的女人享受着与世隔绝的生活方式。****