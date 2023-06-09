# 尝试后台获取 API

> 原文：<https://medium.com/google-developer-experts/experimenting-with-the-background-fetch-api-ffc5bc1f55c5?source=collection_archive---------1----------------------->

![](img/ad0289e4bde57c60cd728944960b6509.png)

随着更多使用后台驻留工作器的方法的出现，服务工作器 API 正在扩展。我以前写过关于[推送通知](https://www.twilio.com/blog/2016/02/web-powered-sms-inbox-with-service-worker-push-notifications.html)和[后台同步](https://www.twilio.com/blog/2017/02/send-messages-when-youre-back-online-with-service-workers-and-background-sync.html)的文章，最近我探索了非常新的[后台获取 API](https://github.com/WICG/background-fetch) 。以下是我对它的了解。

# 下载和上传

后台获取 API 试图解决两个问题:

*   当服务人员正在下载大文件到缓存，而用户导航离开时，即使使用`event.waitUntil`服务人员也可能最终被杀死
*   当上传文件并且用户导航离开时，上传被中断并且将失败

后台获取 API 允许开发人员在单个页面的上下文之外执行和控制对大文件或文件列表的获取。这可以增加成功上传和下载的可能性，并允许服务人员缓存结果。

在编写本文时，这个 API 可以在 chrome://flags 设置中打开了实验性 web 平台功能标志的情况下在 Chrome 中进行测试。

# 测试一个例子

为了试图理解 API 是如何工作的，我决定构建一个简单的概念证明。在 Glitch 上可以找到[示例，或者您也可以](https://fan-hubcap.glitch.me/)[从 GitHub](https://github.com/philnash/service-worker-background-fetch) 检查并运行源代码。我不想处理大文件，所以这个例子试图加载一个有 10 秒延迟的图像(毛刺为 4.5 秒)。

有一个服务工作器被设置为直接从缓存中提供图像(如果存在的话),否则它会转到网络。

```
// sw.js
self.addEventListener('fetch', function(event) {
  if (event.request.url.match(/images/)) {
    event.respondWith(
      caches.open('downloads').then(cache => {
        return cache.match(event.request).then(response => {
          return response || fetch(event.request);
        });
      })
    );
  } else {
    event.respondWith(caches.match(event.request));
  }
});
```

相当标准的服务人员的东西到目前为止，这里是新的东西。

点击“开始下载”按钮将启动后台获取。提取被标记为“大文件”(我觉得很有创意)。当后台获取正在进行时，您可以离开页面，下载将继续在后台进行。

```
// index.html
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js').then(function(reg) {
    const button = document.getElementById('download');
    if ('backgroundFetch' in reg) {
      button.addEventListener('click', function(event) {
        reg.backgroundFetch.fetch('large-file', [new Request('/images/twilio.png')], {
          title: 'Downloading large image'
        }).then(function(backgroundFetch) { console.log(backgroundFetch) });
      })
    }
  });
}
```

下载完成后，服务人员会收到`backgroundfetched`事件。该事件有一个`fetches`属性，它指向包含每个请求和响应的对象数组。代码循环通过`fetches`(尽管在这种情况下我们知道只有一个)并将响应放入缓存。

```
self.addEventListener('backgroundfetched', function(event) {
  event.waitUntil(
    caches.open('downloads').then(function(cache) {
      event.updateUI('Large file downloaded');
      registration.showNotification('File downloaded!');
      const promises = event.fetches.map(({ request, response }) => {
        if (response && response.ok) {
          return cache.put(request, response.clone());
        }
      });
      return Promise.all(promises);
    })
  );
});
```

现在，当请求映像时，它将直接从服务工作者缓存中提供，不再有缓慢的下载。

# 关于后台取数 API 的思考

如你所见，实现后台获取 API 并不困难。在这次初步探索中，有一些事件我没有实现，即`backgroundfetchfail`、`backgroundfetchabort`和`backgroundfetchclick`事件。在一个功能齐全的应用程序中，您会期望这样做。

## 音频困难

我最初用一个音频文件开始这个实验，但是发现很难证明我已经成功地将音频文件存储在缓存中。我并不为此责怪后台获取 API，而是在服务工作者获取事件中处理范围请求的[问题。鉴于该 API 对于下载丰富应用程序的大型音频和视频资源非常有用，让开发人员更容易地使用该 API 应该是任何从事该功能的服务人员讨论的重要部分。](https://samdutton.github.io/samples/service-worker/prefetch-video/)

## 出现在下载中

当我在桌面上用 Chrome 测试时，我惊讶地发现文件是以用户可见的方式下载的。我的期望是，该文件将留给服务人员来处理和缓存。相反，它出现在我的下载文件夹中。例如，如果您使用后台获取 API 来获取游戏的资源，我不希望在用户的下载文件夹中丢弃只在应用程序中使用的资源。不过，当我在 Android 设备上测试时，这种情况并没有发生。

## 什么 UI？

在 API 中，有一些地方会影响浏览器 UI，您可以为获取设置标题和图标，并使用`event.updateUI`方法更新标题。然而，在我的桌面或移动测试中，我在任何地方都看不到这个 UI。我只能假设这是正在工作，并将与另一个版本的金丝雀。

# 敬未来

我喜欢玩这些早期的 API，它们有很大的潜力(我也很期待 [Web Share API](https://philna.sh/blog/2017/03/14/the-web-share-api/) 的回归！).对于需要下载大文件或大量文件的应用程序来说，后台获取 API 将是一个很好的选择，比仅仅使用服务工作者缓存有更多的控制。它显然会对视频和音频应用有用，但游戏和虚拟现实体验肯定也会从中受益。

我认为在浏览器方面仍有工作要做，以使这种体验对开发者和用户都是好的，但看到服务人员的进步是令人兴奋的。我的下一个计划是构建一个应用程序，在现实世界中充分利用后台获取 API，以及其他服务工作者特性。关注[我的 GitHub 简介](http://github.com/philnash/)跟踪进展。

如果你对后台获取 API 感兴趣，GitHub 上有[更多关于这个提议的例子和讨论。如果你也对浏览器即将推出这样的功能感到兴奋，请在 Twitter 上给我留言。](https://github.com/WICG/background-fetch)

[*试验后台取 API*](https://philna.sh/blog/2017/07/04/experimenting-with-the-background-fetch-api/) *原载于 2017 年 7 月 4 日*[*philna . sh*](https://philna.sh/blog/2017/07/04/experimenting-with-the-background-fetch-api/)*。*