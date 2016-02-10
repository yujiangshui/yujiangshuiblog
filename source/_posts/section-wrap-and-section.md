title: section-wrap 和 section 的 HTML 结构和命名方式
id: 180
categories:
  - HTML
date: 2014-03-19 07:37:01
tags:
---

命名是个老大难问题，特别是在做页面这块，需要先根据设计稿确定语义化的 HTML 结构并添加上对应的类等。后来我就总结了`section-wrap > section`这样的一套结构命名方式。一步步的来看。

<!-- more -->

## 传统的结构命名

一般的网站设计稿来说，正文主体部分有一个宽度并且在页面中居中。所以通常的做法就是用个 `wrapper` 之类的类或 ID ，赋值宽度和 `margin: 0 auto;` 使其居中，然后再在里面继续做结构。例如下面这个设计稿

![](http://jiangshui.b0.upaiyun.com/blog/2014/03/section0.jpg)

这是去年6月份左右做的项目，是比较传统的设计稿，中间内容居中，然后再在 `wrapper` 中添加诸如 `header、nav、main 和 footer` 之类的结构排列下来。大体的框架如下：

![](http://jiangshui.b0.upaiyun.com/blog/2014/03/section1.jpg)

使用 `main` 类使其居中，再在里面写结构，很正常的做法没有什么问题。然后后来遇到很多类似这样结构的设计稿：

![](http://jiangshui.b0.upaiyun.com/blog/2014/03/section2.jpg)

同样也是主体居中，但不同的是，导航条等区域都是沾满全宽的，这时候再用上面那种外框包裹主体内容的结构，显然已经不合适了。于是就可以将上面的 header 和 nav 两块区域从居中的 wrapper 结构中拿出来，然后设置全宽，再在里面包裹一个居中块。底部也是这样处理，最后再给 body 设置一个背景颜色，就 OK 了。

虽然同样能很好的实现，可是这样真的好吗？

## section-wrap 和 section 的结构和命名方式

没有一套明确的命名方式，就感觉比较乱，每次命名都乱七八糟的，效率低下。后来仔细思考总结出了 section-wrap 和 section 的结构命名方式来处理，直接看下图

![](http://jiangshui.b0.upaiyun.com/blog/2014/03/section3.jpg)

很简单吧，就是将每一个区域分离出来，设置 `section-wrap section-*` 这样的结构，然后再在内部设置一个 `section` 包裹块，用来居中处理。下面详细的来解释一下：

### 命名和结构的语义性

如果是头部的话，会设置关键词 `header`，那么就会生成下面这种结构

    <div class="section-wrap section-header">
        <div class="section"></div>
    </div>
    

其实这种命名的灵感还是来自 HTML5 中的 `<section>` 标签，但为什么不为了语义性而直接使用 `<section>` 标签？原因有二：

1.  `<section>` 标签中需要带有 `<hn>` 标签来表明这个 section 的主要内容，设计稿中通常不会给你说明，自己编写还需要根据区域内容编写，然后隐藏掉。
2.  `<section>` 标签的兼容性问题，考虑到还需要额外加载脚本，还不如舍弃这点语义性的优势。

然而，这种 `section-wrap section-header section` 比较符合人类的语义，一眼看去，就会明白这块结构是一个独立的块，而且是 header 区域内容。

### 结构的 CSS 编写方法

`section-wrap` 这个类其实是没有太大必要的，因为每个区域需要靠自定义的 `section-header` 这个类来规划。但是仍然保留 `section-wrap` 来进行全局的“意外的”样式修改处理，对它的定义将会应用在每一个区域中。

`section-*` 就是来划分不同区域并且进行具体样式编写用的。用它作为一个类似“命名空间”的分割，这样即便是在不同的区域中相同的结构，也可以被灵活的定义成不同的样式而不会产生冲突。这样就可以减少命名的负担，你甚至可以在不同的区域内使用相同的命名，减少命名结构给你脑细胞带来的负担。

`section` 是该结构的主体部分，显然，直接在全局的水平上为它定义：

    .section{
        width: 960px;
        margin: 0 auto;
        @extend .clearfix;
    }
    

根据设计稿的宽度定义这里的 `section` 并且使其居中和清除浮动，这样就创建了一个比较合理的主体包裹结构。

下一步就是在 `section` 中进一步的按照设计稿的要求来编写结构和内容了，这里不再赘述。

### 高效的应用这种编写方式

使用 Emmet 的话，可以直接复制下面这行代码，然后添加一下 `section` 的特殊名字摁下 TAB 键就可以了：

    .section-wrap.section-header>.section
    .section-wrap.section->.section

这行代码已经添加进 [ysass](https://github.com/yujiangshui/ysass/blob/master/html/index.html) 有兴趣也可以直接用 [ysass](https://github.com/yujiangshui/ysass) 来当模板。

如果你也用 Sass、Less 这类预处理编译器的话，一般就可以写出下面这种代码

![](http://jiangshui.b0.upaiyun.com/blog/2014/03/section4.jpg)

使模块更加清晰。

## 使用这种命名方式的优势和意义

我已经在 10+ 个整站项目中应用该命名结构和功能，其中的设计稿也有很大的不同，但是这种结构都应对的不错，所以可以说它是具有一定灵活性的，可以适应大部分的设计需求。

更重要的意义我觉得在于模块化，使用这种结构，明确的将各个部分进行了分割。这样在构造新的页面时，可以直接把需要的模块复制过来，而且也不必在乎 CSS 的冲突。例如：页面a 用到了 `section-header、section-nav、section-main、section-footer` 而 页面b 用到了相同的 `section-header、section-nav、section-footer`，直接从 页面a 复制过来即可，然后再新建一个 `section-content` 来处理 页面b 的主体结构。完全不需要考虑模块之间的相互影响。

同时对于不同结构的等级也有一个明确的划分，使其结构层次关系更加合理明确。

CSS 方面也是如此，使用 `section-*` 来做“命名空间”，即便是懒得命名使用相同的命名或结构，也可以定义不同的样式。同时全局的 `section-wrap` 和 `section` 类，让你修改尺寸或者做响应式的时候，可以做进一步的统一调整。

高手在看这篇文章或许会笑，这么基础的东西也写的这么复杂，用这么多专业名词。毕竟我还是菜鸟，如果你有更好的建议或者经验，欢迎讨论分享~