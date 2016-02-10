title: 常用 APP 的 WebView 内核测试
date: 2015-01-27 11:50:26
id: 240
categories:
	- 其他

---

**注意：通过 UA 探测判断内核方法的准确性有待讨论，因为 APP 可以任意修改 UA，简单的测试，不同平台下差距较大。如果你有好的建议欢迎提出。**

在移动端前端开发中，WebView 占的份额很大，特别是微信前段时间还公开了自己的 JS SDK 使得[基于微信 WebView 的开发越来越有竞争力](http://www.forbeschina.com/review/201501/0040207.shtml)。

但是 WebView 始终是 WebView，是通过手机上浏览器来提供 Web 渲染功能。该测试的主要目的是想要验证以下问题：

* QQ、微信的 WebView 使用的是什么内核？
* 它们跟 Android 原生浏览器有什么关系？
* 安装 QQ 浏览器对 QQ、微信 WebView 有什么影响？
* 安装 Chrome 会对 WebView 有没有影响，跟 Android 原生浏览器有什么关系？
* UC、百度 浏览器使用的什么内核？跟 Android 原生浏览器有什么关系？

了解 WebView 使用的内核对于调试和开发非常有帮助。当你测试你的 WebAPP 时，它可能在你的 Chrome for Android 里面很正常，但并不意味在微信的 WebView 里面正常（因为内核不一定相同）。如果通过测试得知他们使用的内核的版本和规则，那么测试起来就会方便很多，避免无用的测试。

这一点应该在对应 APP 的开发者文档中说明，但是没有搜索到，如果有了解的朋友，欢迎留下评论。

由于手机品牌、操作系统众多，本人测试机不多，所以希望大家有时间、有设备的可以拿起来扫一下填写一下，各种场景的 WebView 全部测试一遍也不会花费你 30 分钟的时间。

测试页面在：[http://yujiangshui.github.io/test-webview/](http://yujiangshui.github.io/test-webview/)

它的 Repo 是：<https://github.com/yujiangshui/test-webview/>

如果你有好的建议，欢迎在 Issues 里面提出。

## 测试方法

在移动端设备上打开测试页面，为了方便，可以扫描下面二维码图片：

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/webview-test-qrcode.png)

打开之后会自动获取你设备的相关信息。需要人工选择的是下面三项：

* 请选择当前使用的手机 APP （会尽量自动判断，如有误需手动修改）
* 是否安装过 QQ 浏览器？
* 标识符

由于需要测试 WebView 与浏览器之间的关系，所以可能会需要你在同一台设备上使用多个软件打开该页面，并选择相应的 APP。希望尽量测试的有：

* Android 原生浏览器（如果测试机是 Android 系统）
* iOS Safari 浏览器（如果测试机是苹果设备）
* 微信 WebView
* QQ WebView
* 安装过 QQ 浏览器，微信 WebView 和 QQ WebView 的变化（这里可能需要麻烦大家卸载或者安装 QQ 浏览器）

如果可以，希望能多测试一下几种：

* UC 浏览器
* 百度浏览器
* QQ 浏览器
* Chrome for Android

这些浏览器是份额较大的浏览器，测试它们的内核版本可以更加清晰它们之间的对比和基情。

之后由于可能要测试多遍，所以需要用一个标识符来表示这些结果是一台设备测试的，你可以随意指定一个标识符，只需要保证同一设备统一即可。

最后点击 “提交结果” 即可，多谢。

## 测试结果

测试结果将在有一定数量的测试用例之后，分析给出结论。届时会更新到本文该部分，并在微博和 Github 有关 repo 上面更新。