title: 再谈弹窗那些事
tags:
  - 弹窗
  - jQuery
id: 128
categories:
  - JavaScript
date: 2014-03-28 01:32:01
---

很早之前对 jQuery 略懂的时候，写过一篇[简单实现弹窗功能](http://www.qianxingzhem.com/post-1758.html)的文章，而且“不害怕”的发表到了[我爱水煮鱼](http://blog.wpjam.com/m/jquery-popup-mask-effect/)上。很基础，很简单，里面的有些做法都是不成熟的，只能说简单的实现了那么一个弹窗效果。

虽然写的很基础很一般，但是从统计数据来看，这篇文章还是有很多人搜索很多人看，再加上到现在过了的这一年多，做过很多遍弹窗功能，对简单的弹窗效果也有了进一步的理解，再次总结一篇更加深入的文章。

<!-- more -->

## 弹窗的实现原理和方法

弹窗通常就是两部分，一部分是半透明的背景遮罩，另一部分就是承载主体内容的区域。当点击某个按钮或者某个地方触发，将隐藏的遮罩和内容通过某种动画效果显示出来。然后点击内容中的关闭或者遮罩区域，就会将遮罩和内容通过某种动画效果隐藏起来。

所以，实现弹窗第一步，就是要写结构和 CSS。

### 弹窗的 HTML 结构和 CSS

既然知道实现方法，一般的结构主要有下面两种（分情况使用）：

第一种，将内容块独立出来，再声明一个 `.overlay` 结构用来做背景遮罩。结构如下：

    <div class="box-login">
        ...
    </div>

    <div class="overlay"></div>
    

这样的话，就可以为 `.overlay` 结构设置半透明的背景图片或者使用 rgba 的背景，然后为 `.box-login` 应用[绝对居中的布局模式](http://coding.smashingmagazine.com/2013/08/09/absolute-horizontal-vertical-centering-css/)或者使用 JS 动态计算宽高或者位置。(当然，这种方法的兼容性 IE8+ 特别在乎的，可以使用 JS 来计算位置，以及用半透明的 gif 图片当背景）。

这种结构的优势是，遮罩层可以被复用，任何需要弹窗的场景，都可以直接用 overlay 这个遮罩层，twitter 等用的就是这种，也推荐这种。

具体代码直接看下面的用 jsfiddle 做的 [Demo](http://jsfiddle.net/yujiangshui/gVyUm/) 

<iframe width="100%" height="300" src="http://jsfiddle.net/yujiangshui/gVyUm/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

第二种，将内容块包裹进 `.overlay` 结构中。结构如下：

    <div class="overlay box-login">
        <div class="section">
            ...
        </div>
    </div>
    

这种结构更加独立一些与其他结构相区别，用 JS 来控制也比较统一比较简单，也比较方便用 JS 等插件生成。[fancybox](http://fancyapps.com/fancybox/) 就是生成这种结构来实现弹窗。

但是这里需要注意，由于内容块是被包裹在 overlay 层的，所以要实现点击 overlay 层取消弹窗的效果，需要在内容块中阻止单击或者其他有关事件的冒泡，这样在内容块中的操作，就不会触发取消弹窗的效果了。

具体代码如下，或者打开 [Demo](http://jsfiddle.net/yujiangshui/9pMxw/)

<iframe width="100%" height="300" src="http://jsfiddle.net/yujiangshui/9pMxw/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

通常还会为弹窗层中添加一个“关闭”按钮，点击关闭按钮就关掉当前弹窗，一般使用下面代码，因为还需要找图标等，就不再上传图片做 Demo 了：

    $('.close-it').on('click',function(event) {
        $(this).parents('.overlay').fadeOut(200,function(){
            $(this).removeAttr('style');
        });
    });
    

还需要注意一个细节，在用 jQuery 做动画的时候，尽量用回调函数清除 jQuery 为其添加的 style 属性，这样可以避免一些意外的问题，流过血的教训。

## 注意弹窗的内容

其实对于弹窗效果的内容块，初始的时候为其应用 `display: none;` 是非常简单而合理的，但实际应用还会出现一些意外情况，你需要注意弹窗的内容。

[前段时间的项目](http://yujiangshui.com/recent-projects-review/)中，弹窗里面的内容比较复杂，有幻灯片、图表之类的插件，结果出现了问题，幻灯片和图表区域内容会出现错位、变形等情况。后来测试了一下发现了原因，因为这些插件在初始化的时候，需要计算外框或者相关元素的尺寸位置等，如果你为其设置了 `display: none;` 之后，插件初始化就获取不到正确的数据，就错位或者失效了。

所以使用 `display: none;` 来隐藏弹窗也是需要分情况讨论的，有些情况下，使用 `z-index: -1;` 的方式隐藏会更加合理，如果需要做动画，可以设置 `opacity` 或者当页面加载完之后，相关插件已经获取相关结构的尺寸等之后，使用 `setTimeout` 函数为其增加 `display: none;` 相关的类也可以。

## 提升弹窗的用户体验

上面的弹窗还是比较基础和笼统的，一般来说，根据弹窗内容块的布局，还可以分两类：

### 弹窗后允许页面滚动

这种方式通常使用 `position: absolute;` ，可以看看我做的这个 [Demo](http://jsfiddle.net/yujiangshui/XJTuc/)。主要用来应对弹窗内容很大很多的情况，超过了屏幕的宽高需要产生滚动条来方便浏览者查看。有一些图片弹窗插件使用这种方式，使用 JS 动态计算弹窗内容块的位置，这样即便是内容块很大，也不会造成信息缺失。

但是居中往往需要 JS 配合或者进行位置、尺寸处理，会稍微麻烦一些。

### 弹窗后不允许页面滚动

你可以通过为内容块设置 `position: fixed;` 使其虽然滚动了，但内容块仍然居中显示，给人一种没有滚动的感觉，还是刚刚的 [Demo](http://jsfiddle.net/yujiangshui/XJTuc/) 注释掉然后修改一下就可以看到。

也可以为 `html、body` 标签设置 `overflow-y: hidden;` 属性，使其滚动条消失，无法滚动。

通常来说，一般都要使用 hidden 来隐藏滚动条禁止滚动，然后使用 absolute 或者 fixed 就看你自己的需求来选择。但是，对 `html 或 body` 设置 `overflow-y: hidden;` 之后，由于滚动条没有了，宽度变宽，页面内容会整体偏移一点，虽然只是一点，但是很明显。**这简直太影响用户体验了！！**

### 去掉滚动条但是避免页面内容偏移

知乎上前段时间也提到了这个问题：[如何禁止浏览器滚动条滚动，但是又不让它消失？](http://www.zhihu.com/question/21865401)。其中比较简单方便的就是 [TQ](http://targetkiller.net/) 学长的回答，但是实际测试并不完美，因为 chrome 浏览器的滚动条是 15px 像素宽，而 firefox 浏览器是 17px 像素。如果统一设置成 17px 的话，chrome 等浏览器中显然还会偏移 2px ，虽然尽力完善了，但还是有点小偏移，受不了。

既然不同浏览器里面滚动条宽度不同，那可否先用 JS 检测操作系统和浏览器，然后再根据判断的浏览器等设置不同的偏移量？大体思路是对的，但是方法是错的。检测操作系统和浏览器，要判断的情况太多，代码太过于复杂。于是这个问题就暂时放在这里了。

偶然刷 twitter 的时候，触发弹窗效果的时候，偶然看到了他们也是采用这种方式隐藏滚动条并且保证不会偏移，经过测试各个浏览器中效果完美。于是分析了一下他们的代码，下面是他们的函数：

    function ScrollbarWidth() {
        this.calculateScrollbarWidth = function() {
            if ($("#scrollbar-width").length > 0) return;
            var a = $('<div class="modal-measure-scrollbar"/>').prependTo($("body")),
            b = $('<div class="inner"/>').appendTo(a),
            c = a.width() - b.width();
            a.remove(),
            $("head").append('<style id="scrollbar-width">        .compensate-for-scrollbar,        .modal-enabled,        .modal-enabled .global-nav-inner,        .profile-editing,        .profile-editing .global-nav-inner,        .overlay-enabled,        .overlay-enabled .global-nav-inner,        .grid-enabled,        .grid-enabled .global-nav-inner,        .gallery-enabled,        .gallery-enabled .global-nav-inner {          margin-right: ' + c + "px      }      </style>")
        }
    }
    

精简一下主要代码就是

    var a = $('<div class="modal-measure-scrollbar"/>').prependTo($("body")),
    b = $('<div class="inner"/>').appendTo(a),
    c = a.width() - b.width();
    a.remove();
    $("head").append('<style id="scrollbar-width"> .compensate-for-scrollbar{ margin-right:' + c + 'px } </style>');
    

大体意思就是新建了一个包裹的结构，然后两个宽度相减得到滚动条的宽度，然后输出到 head 中。当有弹窗发生之后，就为 html 标签赋予相应的类来 margin-right 一下，避免滚动条消失引起的内容偏移。

Great！这才是正解，显然这两个结构需要赋予一定的 CSS，目的要使 `.modal-measure-scrollbar` 产生滚动条，而 `.inner` 是不包括滚动条的全宽，他们的差值正好就是滚动条的宽度！

twitter 中对它们应用的样式如下：

    .modal-measure-scrollbar{
        position: absolute;
        height: 100px;
        width: 100px;
        top: -300px;
        left: -300px;
        overflow: scroll;
        z-index: 1000;
        overflow-y: scroll;
    }
    .modal-measure-scrollbar .inner{
        height: 200px;
    }
    

不需要解释了吧。

后来又测试了 fancybox 插件，它的方法更加简单通用，具体代码如下：

    w1 = $(window).width();
    H.addClass('fancybox-lock-test');
    w2 = $(window).width();
    H.removeClass('fancybox-lock-test');
    $("<style type='text/css'>.fancybox-margin{margin-right:" + (w2 - w1) + "px;}</style>").appendTo("head");
    

其中 H 变量是 `$('html')` ，大意就是先检测现有宽度，然后再为 html 增加一个类，再检测，然后复原去掉类得到滚动条宽度。大体想一下也可以知道 `.fancybox-lock-test` 类的代码是这样的：

    .fancybox-lock-test {
        overflow-y: hidden !important;
    }

这种方法更加巧妙简单，不需要创建临时的 DOM 结构，所以强烈推荐在项目中使用这种方式判断。

此外，还有第三种方法，是来自 QQ 空间的。QQ 空间的相册之类的，也是采用弹窗的，查看了一下他们的实现方式，就是使用的固定的 17px 的偏移值，在其他浏览器中也是 17px 像素。但是使用中很难看到内容的偏移，原因是背景遮罩层太黑了，透明度不高，所以很多细节就忽略掉了。如果你的背景遮罩不是很透明，显然也就不需要处理这了~

## 扩展阅读

推荐 [前端博客](http://qianduanblog.com/) 对于检测滚动条提供的原生 JS 实现方法：[计算滚动条的宽度](http://qianduanblog.com/3245.html)