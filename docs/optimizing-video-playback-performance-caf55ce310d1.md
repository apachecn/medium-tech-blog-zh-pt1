# 优化视频播放性能

> 原文：<https://medium.com/pinterest-engineering/optimizing-video-playback-performance-caf55ce310d1?source=collection_archive---------4----------------------->

Norbert Potocki |视频和图像平台 Pinterest 工程经理

确保所有 Pinterest 用户都能获得出色的视频播放体验是一项巨大的工程挑战。在本帖中，我们将讨论回放性能的各个方面，以及我们的工程团队如何优化它。请继续阅读！

![](img/57a960bfb4d3dd2e87bd1322c15736a8.png)

# 回放性能的重要性

视频的播放速度对视频的参与度有很大影响。外部[分析显示](https://www.akamai.com/us/en/multimedia/documents/white-paper/maximizing-audience-engagement-white-paper.pdf)视频加载中断或缓慢会导致用户放弃体验。三秒钟的延迟会让你损失 13%的浏览量。这需要大量的基础设施和微调，以始终满足性能要求。在网络基础设施差的地方尤其如此。用户拥有的智能手机类型和数据套餐也会产生很大影响。

# 视频性能差的五个症状

为了做出明智的决策并设定有意义的性能目标，我们需要了解视频在所有客户端平台(web、Android 和 iOS)以及各个地理区域的表现。为了做到这一点，我们开始逆向工作，确定性能瓶颈是如何向 Pinners 显现的，以及我们如何测量它们。

## 症状 1:视频加载缓慢

有多少次，当你等待视频加载时，看着旋转器转动，你变得焦躁不安？我们都经历过。为了捕捉和衡量这个问题的规模，我们使用了一个叫做**感知等待时间(PWT)** 的指标。它测量用户想要观看视频(点击视频，将自动播放的视频滚动到视窗中)和播放开始之间的时间。我们很少关注其他指标:

*   **到第一帧的时间(TTFF)** :从发送视频请求到加载视频的时间(即接收到足够的数据来渲染第一帧)。如果客户端实现预取或者在本地存储视频的副本，则它可以显著高于 PWT。
*   开始播放所需的**自适应比特率(ABR)** 流段的数量。(我们将在后面更详细地介绍这一点。)
*   网络级度量:连接性能分析的技术度量。这些包括 DNS 解析时间、连接握手时间、下载速度和带宽利用率。

![](img/54276863a5acc50709f63d57a00c0437.png)

*Waiting for a video to load can be annoying — displaying a spinner doesn’t help much.*

## 症状 2:视频随机暂停

你见过视频中途突然停顿的吗？发生这种情况有几个原因，我们尝试用这些指标来衡量这个问题:

*   每次播放的暂停次数:视频播放暂停并进入“缓冲”状态的频率。当我们聚合多个回放会话时，这是由视频长度加权的。
*   恢复时间(滞后长度):从用户看到暂停的视频到视频恢复需要多长时间。

## 症状 3:画质低

性能差的另一个症状是图像质量低，这通常是因为没有足够的带宽来传送更高质量的视频流。这也可能是视频优化不佳或基础设施落后的结果。为了确定是哪种情况，我们回顾了以下指标:

*   视频变体使用:测量每个视频变体被使用的次数，并在单个 ABR 段级别进行计数。
*   播放过程中的变量变化数量:显示给用户的视频多长时间改变一个变量。
*   带宽利用率:视频流使用了多少可用带宽。

## 症状 4:搜索(拖动)视频时，需要一段时间才能恢复

当您拖动并释放搓擦器时，您希望视频会立即恢复。往往不是这样，你要等回放再开始。我们用一种叫做特技模式延迟的方法来监控这种延迟。

![](img/842b026c30ffb190565635c477dd3571.png)

*Scrubbing should be a snappy experience.*

## 症状 5:视频与音频不同步或不能一起播放

这种行为可能是由多种原因造成的，但通常是由于终端设备动力不足。为了更好地理解这个问题，我们使用有关设备功能的信息，包括我们使用的编解码器的硬件支持数据和播放期间的设备资源利用率。

# 我们的补救措施:微调自适应比特率(ABR)流

为了实现高效的流传输并缓解上面列出的一些问题，我们使用了[自适应比特率流传输](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，这是当前视频传输的行业标准。ABR 流媒体的两个最流行的实现是苹果的 [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) 和 [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) 。这两种技术都是通过将源视频文件编码成具有不同比特率的多个流来工作的。然后，这些流被分成具有相似持续时间(例如，几秒钟)的较小片段。然后，视频播放器根据可用带宽和其他因素在流之间无缝切换。这允许视频在信号差时仍然播放(以较低的质量),并在信号增强时跳转到较高质量的流。

# 调整 ABR 流

使用 ABR 的棘手部分是微调每个流的配置，以便它们在产品中以最佳状态运行。值得一提的是，没有哪种单一配置可以通用于所有产品。这是一个高度特定的产品设置，随着时间的推移而演变。在 Pinterest，我们主要处理短的、预先生成的内容(比如 VoD)，这些内容会在用户滚动其主页提要时自动播放。它主要用于带宽有限的各种移动设备。让我们讨论一下可以调整哪些参数以满足该标准。

整个过程本质上有点“反复冲洗”。您可以选择起始参数，观察回放在实时流量上的表现，并根据这些信号调整配置。随着网络基础架构和设备功能的不断变化，您可能需要继续更新配置。收集上述指标确实简化了流程。

## **流数、分辨率和比特率**

我们最初决定提供多少个流以及使用哪些解决方案是基于两条信息。首先，我们充分了解了视频可能出现的所有产品表面及其尺寸。其次，我们使用了我们经营的主要市场中可用的[网络基础设施的知识来设置初始比特率。](https://www.akamai.com/uk/en/multimedia/documents/state-of-the-internet/akamai-state-of-the-internet-report-q1-2016.pdf)

一旦我们推出了初始设置，我们就开始收集指标:PWT、TTFF、视频播放器更改已使用流的频率以及我们的流与可用带宽的接近程度。基于这些信号，我们进一步调整我们的设置，以尽量减少 PWT。

## **帧速率**

为视频流选择的帧速率会显著影响平滑回放所需的必要带宽。这也可能改变视频的感觉。有三种常用的流帧速率。

*   24fps:历史上用于电影院。在现代设备上可能有点不稳定。
*   30fps:这是大多数流服务的标准，它在大多数类型的内容中都表现良好。快速动作序列(例如，体育)和当你想要“真实生活”效果时的情形(例如，新闻、戏剧、自然视频)可以受益于更高的帧速率。
*   60fps:这非常接近人眼能够处理的信息量。超过这个水平通常没有多大意义，除非您打算在播放期间减慢视频。它通常用于快节奏的场景，当你想要一个“真实生活”的感觉到你的视频。这不是一个很好的电影体验，因为它看起来像真实生活，并且暴露了太多的细节(例如化妆、服装、场景的缺陷)。因此，观众不会感受到 30 帧/秒(甚至 24 帧/秒)电影的“电影魔力”。然而，这对于快速动作游戏来说很棒。

对于我们的用例来说，30 fps 的帧速率作为经验法则非常有用。随着我们的媒体语料库的发展，我们计划向一些媒体文件添加 60fps 的变体。值得一提的是，降低最低质量比特率的帧速率是有意义的。对于 Pinterest，我们以 15fps 的速度对视频进行重新编码，因为我们发现，如果视频暂停，用户会比看起来有点不稳定的视频更不满意。

![](img/31a70e390dafdbb5e725dee0e401c469.png)

*Choosing the best frame rate for your videos will help achieving a desired visual effect.*

## **片段大小和持续时间**

互联网上有很多推荐的片段时长。很长一段时间[苹果推荐的](https://developer.apple.com/library/ios/technotes/tn2224/_index.html)段时长是 10s，最近他们把 HLS 流的时长修改为 6s。最适合 Pinterest 的分段时长是 4s。我们使用以下准则来做出这个决定:

*   一些视频播放器(最著名的是 iOS AVFoundation)会在播放开始前加载一些视频片段。对于高质量的流和长段，这可以产生非常高的 PWT。
*   数据段越长，播放时间就越长，以适应不断变化的网络条件。
*   使数据段非常短(例如 1 秒)会导致对服务器的大量请求，从而增加网络和处理开销。
*   根据经验，每个段的字节大小应该低于 1MB。这导致大多数 CDN 提供商的驱逐率较低。

## **视频和音频编解码器**

决定使用哪种编解码器时，有两个限制因素:

*   首先是你用于流媒体的技术。例如，HLS 强迫您坚持使用 h.264 视频编解码器和少数受支持的音频编解码器之一。DASH 可以更加灵活，因为它与编解码器无关。
*   第二个因素是用户设备提供的功能。Pinterest 在全球范围内都可以使用，我们的许多用户都使用处理能力有限的旧设备。为了他们，我们使用最广泛支持的配置 h.264 编解码器，主配置文件，在 3.1 级伴随着 HE-AAC v1 和 v2 编解码器的音频通道。

## **微调**

一些最后的小调整包括选择用于第一段回放的流的质量。为了最小化 PWT，我们在播放的前四秒使用中等质量的流，而不是高质量的流。

另一个设置迫使我们的编码器在每个片段的开头放置一个 IDR 帧。这使得视频播放器在开始渲染帧之前不必加载整个片段，并大大减少了 PWT 和恢复时间指标。

我们还确保我们的流媒体技术支持何时启用[特技模式](https://en.wikipedia.org/wiki/Trick_mode)。HLS 和 DASH 都支持此功能，并支持高性能清理，最大限度地减少特技模式延迟度量。

最后的优化是确保我们的交付基础设施速度极快。为了保证这一点，我们使用几个 CDN 提供商，并为每个用户选择目前最快的一个。

# 最后的想法

在大规模产品中实现高性能的视频播放需要大量的调整。我们强烈建议您尝试不同的流技术和设置，看看哪种设置最适合您的产品。我们也很乐意与您讨论结果，所以请随时联系我们的视频和图像平台团队！

*鸣谢:视频&图像平台团队成员:张睿、Jared Wong、Tianyu Lang、Nick DeChant 以及其他贡献者:交通团队的 Josh Enders、Kynan Lalone 和核心体验团队的 Dom Bhuphaibool、柳斌和 Edmarc Hedrick。*