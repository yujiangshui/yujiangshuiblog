title: 仅仅 IE8 有效的 CSS hack 写法
id: 119
categories:
  - CSS
date: 2014-01-18 07:36:48
tags:
---

最近做项目的时候，引用了 Google Font 的在线字体，估计是因为浏览器对字体的渲染方式不同或者是字体格式不一样，结果导致 IE8 中导航是错位的，而 IE9 和 IE10 中是正常的。

<!-- more -->

那么就需要单独为 IE8 进行一些调整，于是就需要 IE8 的 CSS hack。一看到这里，足够容易了吧？网上流传已久的 IE8 CSS hack 就是在属性后面加上 `\9` 或者 `\0`，代码如下：

    color:#FFF\0;                    /* IE8 */
    color:#FFF\9;                    /* 所有IE浏览器(ie6+) */
    

加上之后，发现 IE8 确实是没有问题了，但是 IE9 和 IE10 却出现了问题，上面的 hack 同样对 IE9 和 IE10 起作用。

好吧，这个 CSS hack 不是 IE8 特有的。那怎么做才能只针对 IE8 做 hack 呢？

这里的思路需要稍稍转换一下：可以先应用 IE8 的 hack ，与非 IE 浏览器分开，然后再应用一些 CSS3 之类的 IE8 不支持的选择器来重新覆盖一遍，让支持新选择器的 IE9、IE10 正常显示。符合条件的常用选择器有 `:root`，于是就可以写出下面代码：

    .section-nav li a {
      display: block;
      padding: 10px 13px;
      padding: 10px 7px  10px 7px \9;
      border-right: 1px solid #d7d7d7;
    }
    :root .section-nav li a {
      padding: 10px 13px 10px 13px;
    }

这样就做到了只对 IE8 浏览器起作用的 CSS hack，这种方法后来搜索了一下，早就有人提到了，写本文主要是介绍一下这个解决浏览器兼容性问题的思路吧。