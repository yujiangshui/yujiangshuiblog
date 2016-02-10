title: 三个 HTML5 不常见特性简介
tags:
  - HTML5
id: 147
categories:
  - HTML
date: 2014-02-19 12:36:22
---

都已经到了 2014 年了，再来说 HTML5 的新特性，实在是够晚了，不过 [Denis](http://blog.wpjam.com/) 说的好，即便再烂的文章，只要不误导，有总比没有强（不是原话，我润色了一下~）。

<!-- more -->

## DNS 预解析缓存

众所周知，解析 DNS 是网站性能优化的比较重要的一部分，虽然加载时间不太长，但是很难压缩起来。特别是为了并发下载资源而使用多个 CDN 域名来加载资源的大型网站，更不可忽视，每加载资源之前都要先进行 CDN 域名的 DNS 解析转换。

如果采用 DNS 预加载，支持该功能的浏览器就会提前对该域名进行 DNS 解析并且缓存一下，而不会在需要请求资源再进行解析。而且这个功能应用实在是太简单：

    <link rel="dns-prefetch" href="http://cdn.staticfile.org/">
    <link rel="dns-prefetch" href="//www.google-analytics.com">
    

具体之前草草翻译过一篇文章，有兴趣可以大体略过：[使用预加载提速你的网站](http://www.qianxingzhem.com/post-1900.html)

## 资源预加载

资源预加载有很多办法，例如常见的图片预加载，有采用 CSS 的背景图片来预加载，大部分还是用 JS。目前 HTML5 提供了专门的资源预加载方法，有两个属性：prefetch（预读取）和 prerender（预渲染），分别被 Firefox 和 Chrome 浏览器支持。

### prefetch 预读取

预读取就是很常见的资源预加载，当前页面加载完成之后，就会在后面偷偷的下载你指定的资源，一般是 JS 、CSS 和 图片 这类的，也可以下载页面：

    <link rel="prefetch" href="http://yujiangshui.com/" />
    <link rel="prefetch" href="http://yujiangshui.com/wp-content/themes/jiangshui/images/avatar.jpg" />
    <link rel="prefetch alternate stylesheet" href="mozspecific.css" />
    

目前 Firefox 浏览器支持这个功能，详情请看：[Link prefetching FAQ](https://developer.mozilla.org/en-US/docs/Link_prefetching_FAQ)。

### prerender 预渲染

这个更厉害了，不仅偷偷的提前下载，而且还给你渲染出来，当用户点击链接的时候，立刻给你展现出来。

    <link rel="prerender" href="http://yujiangshui.com/" />
    

目前 Chrome 支持这个功能，详情请看：[Web Developer's Guide to Prerendering in Chrome](https://developers.google.com/chrome/whitepapers/prerender)。

搜素引擎其实是最需要这种预读取的功能的，因为他们非常确定用户下一步要打开的页面（搜索结果页面），所以当用户输入搜索内容的时候，就可以提前把搜索结果页面的资源提前加载，而且应用之后，效果十分明显。

目前兼容性是个缺点，貌似只有 Chrome 和 Firefox 支持，而且用的 rel 属性是不同的，如果你想同时兼容两个浏览器，可以写成下面这样：

    <link rel="prefetch prerender" href="http://yujiangshui.com" >
    

此外，当然为了安全没法跨域预加载资源，可能没法用 CDN 了。

## Download 属性

HTML5 的 Download 属性用来强制浏览器下载对应文件，而不是打开。Chrome 和 Firefox 等浏览器太过于强大，也许是为了增强用户体验，当用户点击的资源文件可以被它们识别的时候（例如 pdf 会直接在浏览器打开，mp3、mp4 等媒体直接用浏览器内置播放器播放）。但有时候，用户其实是希望直接下载而不是在浏览器上看看，这时就可以加上这个属性，属性值会对下载的文件重命名：

    <a href="downloadpdf.php" download="download.pdf">点击直接下载并保存成 download.pdf 文件</a>

你可以点击下面两个链接试下看：[没有加 download 属性的常规 pdf URL](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf) | [加 download 属性的常规 pdf URL（保存成 Ecma-262.pdf）](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf)

如果你确定这个资源是用户肯定会下载的，就可以加上这个属性，还可以用 JS 或者手动改变想要保存的文件名。

## 资料扩展

HTML5 还有很多其他特性，但是看了很长时间书和各种资料，很少见到上面三个又比较实用的属性，拿出来分享一下。关于本文提到的几点，有兴趣深入的可以看一下：

1.  [Web Developer's Guide to Prerendering in Chrome](https://developers.google.com/chrome/whitepapers/prerender)
2.  [Link prefetching FAQ](https://developer.mozilla.org/en-US/docs/Link_prefetching_FAQ)
3.  [HTML5 Link Prefetching](http://davidwalsh.name/html5-prefetch)
4.  [HTML5 PREFETCH](http://www.rocketmill.co.uk/html5-prefetch)