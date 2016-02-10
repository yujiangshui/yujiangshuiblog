title: table 那些事
tags:
  - table
id: 156
categories:
  - HTML
date: 2014-03-04 00:08:27
---

table 这个 HTML 标签，是一个很有传奇色彩的标签。它经历过大起大落，走过巅峰，又被人深深唾弃，但仍然不气不馁，在 EDM网页 、居中、数据展示等需求中发挥着不可替代的作用。很多人认为那就是个用 DW 拖拽出来的结构，太简单了，却无法手写出一个完整符合语义性的表单结构。

<!-- more -->

所以，今天就来好好研究一下这个 table。

## table 的使用范围

学习 table 标签之前，显然要先明白应该在什么地方使用 table。

由于 table 便于可视化的布局，使用诸如 DW 这样的工具就可以“画”出来，使用 PS 也可以直接导出切图。兼容性好，即便是低端浏览器也不会遇到错位情况。不需要 CSS 就有丰富的外观设置等等特性。导致 CSS 不发达的几年前，网络上到处都是使用 table 布局的网站。后来 CSS 发展到足够应用了，table 所有的种种弊端显然不再适合用来布局。于是有些人又谈 table 色变，唯恐自己项目里面出现 table。其实 table 有一些不可替代的作用：

*   **承载数据**。显然，表格的产生，就是为了更好的存放和显示数据。如果需要在网页中显示一个数据列表，显然需要用到 table。
*   **EDM 网页**。EDM 就是电子邮件营销，往别人的邮箱里发送邮件。由于邮箱的服务商会做出一些限制，再加上接收邮件的客户端能力的差异，导致日常的网页无法用在 EDM 上。而 table 的一些特性，显然最符合 EDM 的需求。
*   **居中等 hack 用法**。table 的特性可以及其方便的垂直和横向居中里面的内容，而不需要使用 hack 等。

## table 的 HTML 结构

还是先从 table 本身的 HTML 结构入手吧。无论是多好的资料，都是经过二次加工的资料，或多或少都会有点误差或者残缺的东西。想要了解完整的 table 的 HTML 结构，显然要去看一下 [W3C HTML4 文档中的 tables 章节](http://www.w3.org/TR/1999/REC-html401-19991224/struct/tables.html)。但是文档也有一些坏处，里面有些标签已经被废弃，涉及视觉的那些属性，也不再赞成使用（使用 CSS 来替代），下面就筛选出一些必要的标签和属性来说一下。

### table 标签

#### table 属性

`<table>` 就是表示表格的标签，它主要有 `summary` 属性，用来表示当前表格的内容概要。主要是用来给屏幕阅读器和视觉有障碍的人用的。至于其他属性，都不推荐使用。

#### table 的渲染过程

当浏览器遇到 table 的时候，解析渲染的步骤如下：

*   确保 `summary` 发挥作用。每个 table 都应该提供这样一个属性来让视觉障碍阅读器可以获取相关信息。
*   如果 `<caption>` 标签定义了，渲染这个标题。
*   如果表单定义了头部（header）和尾部（footer），那么先渲染头部，再渲染尾部。因为头部和尾部通常包含重要的信息，所以需要先渲染解析。例如，如果表单的“客户端”不是浏览器，而是打印机这类的，当表格太长的时候，一张纸打印不开，就需要分开。这时候虽然表格分开了，比较智能先进的打印机，会在每一页的上面插入表格头，下面插入表格尾部。
*   计算表单中的列数。表格的行数当然就等于表格中 `<tr>` 标签的数量了。
*   通过 `column group` 声明来划分列组。
*   渲染单元格，一行一行的渲染在 表格头 和 表格尾 之间，并且通过 CSS 的相关声明加上样式。

### caption 标签

`<caption>` 标签的含义是当前表格的标题，通常是 `summary` 属性的更浓缩版。

它位于 `<table>` 结构里面的最顶端，因为按照上面的解析渲染步骤可知，这个标签需要优先解析。

### thead、tfoot、tbody 标签

这三个标签比较类似，放在一起说。其中比较常用到的就是 `<tbody>` ，但另外两个也是不可或缺的。

#### thead 标签

表示表格的头部相关信息。如果是一份旅游出行名单的话，通常就是 编号、姓名、联系电话和地址等。用来表示当前列的内容是什么。

#### tfoot 标签

表示表格的底部信息。如果是一份旅游出行名单的话，就是一些 签名、领队联系人、日期 等等信息。

#### tbody 标签

表示表格的主体信息内容，如果是一份旅游出行名单的话，那内容就是一行一行的个人信息。

#### tfoot 要放在 tbody 结构的上面

这是一份大概的表单结构：

    <table summary="这是一个关于旅客的个人资料信息登记表格，主要内容是XX旅行计划的旅客个人资料以及领队电话。">
        <caption>旅客统计</caption>
        <thead>
            ........
        </thead>
        <tfoot>
            ........
        </tfoot>
        <tbody>
            ........
        </tbody>
    </table>
    

比较容易产生疑问的地方就在于，为什么 foot 却在 body 上面？再回头看看表格的渲染过程，就明白了，`<tfoot>` 中往往携带比较重要的信息，需要优先让客户端接收处理，为了避免 `<tbody>` 内容太多导致长时间无法加载 `<tfoot>` 信息，所以在结构上放在上面。

但是浏览器在渲染的时候，还是会将 tfoot 的内容放在 tbody 的下面。

### tr 标签

`<tr>` 标签表示一行。包裹在里面的单元格处于一行。

### th、td 标签

这两个标签就是承载信息的单元格，它们有以下常用的属性：

*   **scope** 表明当前单元格保存的是所在行或所在列的“头部说明”信息。属性值主要有：row 和 col。分别表示当前单元格保存的内容是当前 行 和 列 中的重要头部信息。
*   **abbr** 这个属性提供了当前单元格内容的缩写。后面会提到。
*   **rowspan** 表示当前单元格占几行高度，默认值是 1，表示占一行。
*   **colspan** 表示当前单元格占几列宽度，默认值是 1，表示占一列。这两天属性配合使用来实现合并单元格的功能。

#### th 标签

`<th>` 标签保存的内容，是当前列或者当前行的“头部说明”信息。通常在 `<thead>` 结构中的单元格，就要用到 `<th>` 来实现。如果是一个二维表格，在 `<tbody>` 中，左数第一个单元格保存的往往是当前行中的“头部说明”，也要使用该标签。

#### td 标签

`<td>` 标签就是最普通的单元格，用来保存各种表格中需要的信息。

table 的 HTML 结构和相关属性，基本就上面那些，很多已经被废弃或者不推荐使用。其中单元格的属性 **rowspan** 和 **colspan** 用起来稍微复杂一些，多写一下就明白了，后面会举一个例子，这里不再赘述。

## table 的 CSS 设计模式

### 居中

table 居中是一个低成本效果好的方法，因为使用 CSS 来进行垂直居中同时要考虑到兼容性，实在是比较麻烦的事情，而 table 可以激活 `vertical-align` 属性，将其赋值 `middle` 即可将其中内容垂直居中。至于水平居中，就可以根据具体情况，使用 `text-align: center;` 或者对外框 `margin: 0 auto;` 来实现。

具体用法请看：[垂直居中的几种实现方法](http://sofish.de/1909)，不再赘述。

### table 常用的 CSS 属性及效果

常规属性：

*   **background** 是正常的，都可以用。
*   **text-align、text-indent** 等文字相关属性，只对单元格有效，但是可以**被继承**。*   **margin** 只在 table 上有效，对 td th 等等无效。
*   **border** 只在 table、td、th、caption 有效，对 thead、tbody、tfoot、tr 等无效。
*   **padding、overflow、vertical-align** 只对单元格有效。
*   **width** 在表格、单元格和列上有效，重要的布局属性，后面详细说。
*   **height** 在表格、行和单元格上有效，但其值只能作为最小高度，因为内容总是会扩大单元格、行以及表格的高度。如果使用 display 将其改成 block 块级元素，就会溢出而不会扩大。

重点属性：

*   **vertical-align** 用在单元格里很神奇的效果，有三种比较特殊的设置方式：top、middle 和 bottom，很明显对应表格顶部、中间和底部。神奇之处就在于，它们会将单元格里面的多行内容都进行对齐。这也是上面居中实现的原理。与之相媲美的 position 方式的居中会脱离文档流，而这种方式却不会。
*   **border-collapse** 设置 table 是分开的边框还是合并起来。table 默认的样式是分开的边框，table 和里面 th、td 的边框之间有缝隙。而设置 `border-collapse:collapse;` 可以去掉空隙，合并起来。
*   **border-spacing** 设置边框之间缝隙的宽度，在 table 默认或者 `border-collapse:separate;` 的时候才有效。[border-spacing 具体 demo](http://www.w3school.com.cn/tiy/t.asp?f=csse_table_border-spacing)
*   **caption-side** 设置 table caption 的位置，有两个值 top（默认）和 bottom，分别放在表格的上面和下面。
*   **empty-cells** 设置是否显示表格中的空单元格，有两个值 show（默认）和 hide，兼容性 IE8+ 。注意，使用此方法的 hide 隐藏，不会像 display 那样，从文档流中去掉，而是像 `visibility:hidden;` 那样，保留占位。[empty-cells 具体 demo](http://www.w3school.com.cn/tiy/t.asp?f=csse_table_empty-cells)
*   **table-layout** 设置 table 显示单元格、行、列等效果的规则。这是最重要的属性，也是最复杂的东西，也是本文重点要讲的。

### table 的布局模式

大体有四个模式，每个模式下对 th、td 的设置和改变，也略有不同。为了简洁明了，我做了一个 [table 的设计模式 codepen](http://codepen.io/yujiangshui/full/blvEp)，欢迎打开：[http://codepen.io/yujiangshui/full/blvEp](http://codepen.io/yujiangshui/full/blvEp) ，这里不再赘述了。

注意查看表单上的挂的类，和具体呈现的效果。我在对应 table 上面也做了一点解析。

多说一句，codepen 真是一个很好的在线 Demo 工具，可以直接编写 Sass、less、coffeescript 等，它会帮你自动解析编译。然后**支持 Emmet** 的语法和操作，简直就像在用 sublime text 一样。像表格这种结构变态的东西，不用 Emmet 而用手写结构，简直要疯掉了。

## table 的响应式处理

table 的自动拉伸特性，比较容易适应各种宽度，但是响应式处理起来还是有一定问题的。因为它虽然可以根据内容和外框的宽度自己调整，但是会缩小到内容宽度，有一个最小宽度值。如果这个宽度比手机屏幕宽度还宽，就会发生溢出。而 table 的数据是一行一行的，不同于其他元素可以取消 float 之类的使其上下排列等。

目前主要有以下几种解决方法：

### 隐藏掉不需要的列。

既然因为宽度太宽导致出现溢出现象，那么想办法减少宽度就好了。表格中不太重要的列，完全可以使用 `display:none;` 来隐藏掉，这样就减少宽度达到目的。

不过会有表格数据损失，对于数据完整性要求严格的表格，显然不合适。

### overflow 产生滚动条

表格太宽导致溢出，我们可以在外面包裹一层 div 之类的结构，为 div 设置一下宽度（百分比等）和 `overflow: scroll;`。然后对 table 设置一个固定的宽度，使其在保证数据展示友好的情况下，有一个固定的宽度。

这样，div 就会产生一个横向的滚动条，手机等设备上就可以通过横向滑动看到完整的表格。

这个方案应该是最好的，但是前提是你可以灵活的设置结构。一般在文章内插入的表格，使用这个方法比较合适，如果是固定的功能性的页面，就只能用下面的方法了。

### 修改 display 重新布局

最近的一个团队的电子商务网站，使用 woocommerce 提供电子商务功能，其中的购物车的响应式处理是比较啰嗦麻烦的地方。因为它用到了表格，数据比较多，而且不能去掉某一部分内容，同时还需要按照设计稿上的要求来。

这样只能拿出杀手锏，使用 display 将 table、th、td 等等，全都设置成 block，这样就可以按照平时的布局那样，使用 float 或者 position 等等来定位。虽然会产生一些冗余代码，但也没有更好的办法了。

还需要注意的是，因为 thead 里面的 th 在响应式之后消失了，所以需要在对应列的 td 使用类似

    td.price:before{
        content: 'Price: ';
    }
    

这样，才能在响应式界面，仍然让浏览者识别出来对应项数据。

下面就是最近项目的响应式处理示意：

![table 的响应式处理](http://jiangshui.b0.upaiyun.com/blog/2014/03/table0.gif)

### 其他方法

1.  [cube](https://github.com/thx/cube) 项目，是一个 **跨终端、响应式、低设计耦合的CSS解决方案**，其中提出了[一个解决响应式表格问题的的 CSS ](https://github.com/thx/cube/blob/gh-pages/src/table.css)。不过我还没有仔细看过他们的代码和实现工作原理。有兴趣的朋友可以去看一下。
2.  忘了是 XX.js 了，国外的产品，作者说是专门来解决响应式的 table 问题的。我看了之后很激动的打开测试，居然是这样的：当小分辨率设备访问 table 的时候，JS 将 table 移除然后留下一个类似“点击打开表格”的链接，点击之后，在新页面打开未经过修饰的 table。然后就流汗了，直接关掉，名字都没记住，太扯蛋了。

如果你还有更好的思路，欢迎交流。

## table 的可访问性和可用性处理

现在可访问性成为前端工程师的必备技能了，大家都能用的网站，才是好网站。下面谈谈 table 的可访问性和可用性处理。

### 保证基本良好完整的 HTML 结构

就是最开始的那些，尽量编写 `summary` 属性，让屏幕阅读器可以获取表格的大体内容。添加 `<caption>`、`<thead>`、`<tfoot>`等标签并注意顺序。

### 声明表格的 header

前面第一部分提到的 th、td 的几个属性，其中 scope 和 abbr 就是两个有关可访问性和可用性的属性。

例如下面这段来自 W3C 文档的示例代码：

    <TABLE border="1" 
           summary="This table charts the number of cups
                    of coffee consumed by each senator, the type 
                    of coffee (decaf or regular), and whether 
                    taken with sugar.">
    <CAPTION>Cups of coffee consumed by each senator</CAPTION>
    <TR>
       <TH scope="col">Name</TH>
       <TH scope="col">Cups</TH>
       <TH scope="col" abbr="Type">Type of Coffee</TH>
       <TH scope="col">Sugar?</TH>
    <TR>
       <TD>T. Sexton</TD>
       <TD>10</TD>
       <TD>Espresso</TD>
       <TD>No</TD>
    <TR>
       <TD>J. Dinnen</TD>
       <TD>5</TD>
       <TD>Decaf</TD>
       <TD>Yes</TD>
    </TABLE>
    

其中可以看到，在最上面的一行 th 中添加了 scope 属性，值为 col ，意思就是当前这个 th 是当前列的 header。然后屏幕阅读辅助器等，就会根据这个属性，来组织信息提供给用户。而其中有个 Type of Coffee 比较长，还可以使用 abbr 属性来指定一个缩写词。

scope 属性另外一个值 row ，很明显就是表明当前行中，被赋值的 th、td 是 header，保存了重要信息。

## 做道练习题吧

这是去年9月份 阿里巴巴校招 的一道**仁者见仁智者见智**的网申题，虽然给人很简单的感觉，但是真正有水平的人写出来的结构跟初学者一眼就能分清楚。

![table 测试题](http://jiangshui.b0.upaiyun.com/blog/2014/03/table1.png)

怎么样，看完本文，来写一下结构吧！下面是我的答案：

    <table summary="杭州西湖三日游出行的人员名单，包括旅客编号、姓名、联系电话、和地址。">
        <caption>杭州西湖三日游出行名单</caption>
        <thead>
            <th scope="col">编号</th>
            <th scope="col">姓名</th>
            <th scope="col">联系电话</th>
            <th scope="col">地址</th>
        </thead>
        <tfoot>
            <td colspan="4">领队签字：</td>
        </tfoot>
        <tbody>
            <tr>
                <td>1</td>
                <td>Jamson</td>
                <td>123-45678</td>
                <td>XX市XX区XX号</td>
            </tr>
            <tr>
                <td>2</td>
                <td>Grace</td>
                <td>123-45678</td>
                <td>XX市XX区XX号</td>
            </tr>
            <tr>
                <td>3</td>
                <td>Dizzy</td>
                <td>123-45678</td>
                <td>XX市XX区XX号</td>
            </tr>
        </tbody>
    </table>

## 扩展阅读和参考资料

*   [W3C HTML4 tables](http://www.w3.org/TR/1999/REC-html401-19991224/struct/tables.html)
*   [《HTML5 与 CSS3 设计模式》](http://book.douban.com/subject/20440003/)
*   [垂直居中的几种实现方法](http://sofish.de/1909)
*   [10 Easy Accessibility Tips Anyone Can Use](http://webaim.org/blog/10-easy-accessibility-tips/)
*   [Cube](http://thx.github.io/cube/)
*   [前端开发神器 Emmet](http://blog.wpjam.com/series/frontend-develop-tool-emmet/)