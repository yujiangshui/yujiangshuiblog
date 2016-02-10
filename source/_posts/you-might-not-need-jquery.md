title: 有了它你可以不再用 jQuery 了
id: 143
categories:
  - JavaScript
date: 2014-02-14 03:15:10
tags:
---

jQuery 是一个非常流行而且功能强大效果好的 JS 库，既然强大又完善，注定文件体积是很大的。尽管各种压缩之后，仍然有 几十KB 大小。

如果你的项目需要些很多 JS ，需要用到 jQuery 的一些封装好的功能，当然要加载 jQuery。但是大部分的普通网站，JS 部分仅仅是实现一些基础的功能，例如：显隐、改变位置、增加减少类来触发交互等等。

<!-- more -->

实现上面的这些需求，只需要 DOM 选择，然后加一些其他的处理代码就可以，在这种情况下，使用庞大的 jQuery 就有点浪费了，于是就有了取代 jQuery 的方案，最近在 [A List Apart](http://alistapart.com/) 发现了一篇文章（[Choosing Vanilla JavaScript](http://alistapart.com/blog/post/choosing-vanilla-javascript)推荐的一个文档 [YOU MIGHT NOT NEED JQUERY](http://youmightnotneedjquery.com/) 很不错，比之前看到的其他方案要好的多。

[YOU MIGHT NOT NEED JQUERY](http://youmightnotneedjquery.com/) 这个文档提供了 jQuery 与原生 JavaScript 的代码的对比，你可以找到你要用的 jQuery 代码，迅速的找到对应的原生 JavaScript 代码，直接复制粘贴就可以用上了。

不过这份文档也有一定的缺陷：

1.  兼容性问题不太好，目前只实现了 IE8+ 以上的兼容性，而 jQuery 是做了很好的平台兼容性。
2.  没有封装一些常用的功能。它只是给出了实现 jQuery 功能的原生 JavaScript 代码，并没有对常用的功能（例如动画）做进一步的封装。

所以，如果你的项目比较注重兼容性，而且要用到大量的 jQuery 封装的方法，还是乖乖的用 jQuery 吧，不过学习一下还是很不错的 ：D 