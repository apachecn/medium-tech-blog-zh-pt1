# leet code——不符合竞争对手的 DFS +记忆指南

> 原文：<https://medium.com/quick-code/leetcode-casual-to-competitors-guide-to-dfs-memoization-3667cdbabf68?source=collection_archive---------2----------------------->

![](img/b09e7c7946ffb0d1c7509d854b65415d.png)

# 什么？

**记忆是保存计算值的技术**

# **什么时候？**

当我们想要保存一个函数的特定输入的计算值时，我们使用记忆化，因为我们知道我们将需要再次计算那个输入。例如，如果我计算 F(1) = 10，那么我们可以把它保存在某个地方，这样当有人问我 F(1)是什么时，我们可以不做任何计算就给他。

# 为什么？

记忆大大降低了算法的时间复杂度。有时很难看出它减少了多少运行时间，但它确实减少了。

# 怎么会？

让我们深入下面的例子，看看有的**和没有记忆**的**的相同问题的解决方案**

# 139.移行点

[https://leetcode.com/problems/word-break/](https://leetcode.com/problems/word-break/)

# 问题:

给定一个**非空**字符串 *s* 和一个包含一系列**非空**单词的字典 *wordDict* ，确定 *s* 是否可以分割成一个或多个字典单词的空格分隔序列。

**注:**

*   词典中的同一个单词可以在分段中重复使用多次。
*   你可以假设字典中没有重复的单词。

**例 1
输入:** s =`catsanddog`
word dict =`["cat", "cats", "and", "sand", "dog"]`
**输出:** `[
"cats and dog",
"cat sand dog"
]`

# 解决方案:(无记忆)

**暴力部队 DFS-o(n * 2^n):**

我们可以通过尝试从 0 到 I 的每次拆分，并更新当前字符串来解决这个问题。例如，对于热狗，我们可以将其分解为以下内容:

DFS(热狗)分解成…
h→otdog
ho→tdog
hot→dog
hotd→og
hotdo→g
hotdog→

我们首先检查左边的单词是否是有效的词典单词。如果不是，那就不要递归。然而，如果是，我们在右边的单词中再次调用 DFS。基本情况是

1.  整个字符串都是一个有效的单词→可以断词
2.  字符串的长度=1，并且不是有效的单词→这种递归是不可能的

**时间复杂度:**我们可以通过下面的归约来分析我们分成子串单词的次数。假设给我们一个长度为 n 的单词，我们可以把这个单词分成 n-1 部分。所以我们可以决定拆分单词的选择数是 2^n-1，因为我们可以在那个分区拆分它，也可以不拆分。此外，在大多数编程语言中，substring()方法的时间是 O(N ),所以总复杂度是 O(n * 2^n)

**DFS+记忆化:O(N)时间+ O(N)空间**

**提醒**:记忆是一种我们缓存(存储)计算值的技术。例如，如果我们计算了 **word_break(dog)** ，那么记忆它将意味着把它保存在某个地方，例如在一个 hashmap 或一个带有键值的数组中:“dog”->true

在你知道你可以使用记忆化之前，要检查的一件事是我们是否多次解决了相同的子问题。让我们看看能否在这个问题中使用记忆化。

当我们调用 DFS(hotdog)时，我们的一些递归调用是 DFS(otdog)和 DFS(dog)。但是，注意，当我们调用 DFS(otdog)时，这最后也是从 DFS(otdog)→check _ if _ word(ot)& & DFS(dog)求解 DFS(dog)。这是这个问题需要记忆的主要暗示。

现在让我们记住:

*   **步骤 1** : **定义状态** —我们需要定义一种有效的方式来表示计算出的值。比如我们应该如何存储 DFS(狗)？是否应该做一个 string - > boolean 的 hashmap？这听起来不错，但是存储每个子串听起来效率很低。因为我们知道 DFS 调用的每个子串都是原始字符串的后缀，所以我们可以只用一个数字来表示状态。我们用整数来定义吧。我们将定义我们的 int[]数组，名为 DP[]，定义 DP[i]如下:I 表示从索引 I 到原字符串结束索引的子串。DP[i] =我们是否能破解这个单词的布尔值
*   **步骤 2:编辑强力代码—** 这是我们定义 DP[]表的地方，在每个 DFS 的末尾添加缓存，在每个 DFS 调用的开始，我们首先检查是否看到了它。我们可以通过检查 DP[i]是否为空值来做到这一点。如果是，那我们以前从未见过。

**时间复杂度:**

我们可以通过计算多少个状态来确定记忆 DFS 的时间复杂度。因为我们的 DFS()调用接受一个整数来定义字符串的索引，并且该整数的范围可以从 0 到 N，所以我们知道有 N 个状态。现在，每个 DFS 调用做 O(N)工作，因为我们迭代每个索引，获得子串，如果可能的话递归。因此，我们的总运行时间是 O(N)

**空间复杂度:**

这只是我们拥有的州的数量。因为我们有 N 个状态，所以时间复杂度是 O(N)。

现在让我们试试每周竞赛 188 中的一个更难的问题

# 1444.切比萨饼的方法数量(硬)

# 问题

给定一个矩形披萨，表示为包含以下字符的`rows x cols`矩阵:`'A'`(一个苹果)和`'.'`(空单元格)，并给定整数`k`。你必须用`k-1`刀把披萨切成`k`块。

对于每一次切割，您选择方向:垂直或水平，然后选择单元格边界处的切割位置，将比萨饼切成两片。如果你垂直切比萨饼，把比萨饼的左边部分给一个人。如果你横着切披萨，把披萨的上半部分给一个人。把最后一块披萨给最后一个人。

*返回切披萨的方式数，这样每块披萨至少包含* ***个苹果。*由于答案可以是一个很大的数字，返回这个模 1⁰⁹ + 7。**

# 解决办法

**DFS +记忆化(更简单的动态编程)——**

这个问题求 DFS +内存化/ DP。如果你不熟悉或不擅长 DP，那你来对地方了。我将一步一步地解释如何在所有面试和大多数比赛中得出最优解并解决任何 DP 问题。让我们开始吧:

**蛮力——回溯/DFS: (TLE): O(K*M^N)** 在任何类似这样的问题中，当我们被要求最小化/最大化某个数字时，我的第一直觉是首先编写一个蛮力解决方案。当然，暴力解决方案会超过时间限制(TLE ),但这是最佳解决方案的垫脚石。
我们可以用下面的方式蛮力:

**状态**:首先，我们需要一种方式来表示我们目前的进度。在这种情况下，这就是我们正在处理的比萨饼的大小。由于比萨饼是一个矩形，我们将用左上角点和右下角点来表示当前的比萨饼切片。这是编程中用两点表示矩形的常用技巧。我们还需要记录我们削减的次数。

**尝试可能的走法:**根据问题，我们可能的走法是横向和纵向分割披萨。因此，我们将这样做。我们会用许多不同的方法来分割比萨饼，并在这些比萨饼切片上递归。对于水平切割，这将简单地遍历每一行，并通过在一个较小的行上重复来“切割比萨饼”。类似于垂直，但有柱。我们也

**基础案例:**如果我们切 k-1 次，那么我们知道我们完成了。我们只需要 k 个切片，所以切割 k-1 次意味着我们划分了正确的次数。

**综合起来:**我们从调用整个披萨开始，即从(0，0)到(m-1，n-1)，cut = 0。然后，我们迭代每个可能的切割，并递归这些新的比萨饼切片。我们需要跟踪的一件事是，每个切口必须包含一个苹果。要在 O(1)时间内确定一个矩形是否有一个苹果，我们可以使用另一个 DP 表。如果不使用 DP，时间复杂度会增加 O(MN)，所以需要实现这个简单的表。这被称为不可变 2D 查询，你可以在这里查看:【https://leetcode.com/problems/range-sum-query-2d-immutable/
一旦我们到达我们的基本情况，即我们已经进行了 k-1 次切割，我们需要检查这个矩形切片上是否有一个苹果。如果是的话，我们给全局返回变量加 1。如果没有，那么就返回并在这里完成递归分支。

**接受:O(MNK) —带记忆的 DFS:**

首先，我们需要做一些聪明的事情。注意我们的蛮力是如何跟踪状态的 5 个变量的。左上角的点，由两个变量行和列组成，右下角的点，也是另外两个变量，第五个变量，我们进行的切割次数。我们可以减少变量的数量来保持状态，通过注意到问题说，对于我们进行的每一次切割，当我们垂直切割时，我们保持右半部分，或者当我们水平切割时，保持下半部分。这意味着我们的比萨饼切片将始终包含点(m-1，n-1)，即比萨饼的右下部分。因此，没有必要再跟踪右下角的点，因为它总是(m-1，n-1)。因此，我们的新状态是左上角的点和切割数。

现在，我们只需要将我们的 DFS 暴力转化为记忆。这是通过保存每个递归调用的值，并在最后返回这些递归调用的总和来实现的。我们还需要将它存储在一个全局 DP 表中，以缓存调用。在每次递归调用的开始，我们首先检查这个状态以前是否已经解决过。如果它已经被归还了。搞定了。你刚刚解决了问题！

# 接触

如有任何问题或代码，请发电子邮件至**samatbryan@berkeley.edu**给我！一定要给这个帖子竖起大拇指，并对未来的上传给予反馈！我通常参加 leetcode 比赛，所以我们会在排行榜上见面。