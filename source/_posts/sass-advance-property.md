title: Sass 进阶
tags:
  - sass
id: 211
categories:
  - CSS
date: 2014-06-02 03:51:28
---

先简单的说一下 Sass 这个东西。CSS 是一些非常基础简单得语句的组合，既然简单的语句，就不可避免的有很多重复，冗余的东西。而且没有传统编程语言变量、控制语句等高级特性，所以造成了 CSS 编写低效，往往需要通过查找替换、大量复制来修改或者编写。

Sass 就是用来弥补这个缺陷，为 CSS 提供额外的高级功能，使其开发更加方便快捷，更加方便管理。所以你需要使用 Sass 的语法来编写代码，然后通过 Sass 编译成具体的 CSS 代码，然后使用。所以这里就需要学习一下 Sass 的语法。

<!-- more -->

Sass 的语法是按照一定规则进行缩进然后编写有关 CSS 属性，与传统 CSS 代码差别较大，学习成本高，于是 Sass 就有了一套 Scss 语法规则。Scss 中你可以直接写 CSS，即便你不会 Sass 的语法特性，直接编写 CSS 代码也是可以通过编译的，你只需要保存成 `.scss` 后缀的文件名即可。

这样学习成本就低很多。

## Sass 基础速成

本文讨论 Sass 进阶有关话题，不谈基础的使用方法和语法规则。网上已经有非常多的相关教程和资料了。

如果你还不太了解，强烈推荐阮一峰老师写过文章 [SASS用法指南](http://www.ruanyifeng.com/blog/2012/06/sass.html)，拿出一个小时，认真看完，拿出编辑器，认真敲一遍，就可以在项目中尝试应用了。

## Sass 进阶

### Sass 的数据类型

Sass 既然有了类似编程语言的功能，自然也就有了简单的数据类型。这里简单的介绍一些 Sass 中的数据类型，因为在后面的讨论中要用到有关的内容。

Sass 中主要有六种数据类型：

*   数字型 (e.g. 1.2, 13, 10px)
*   文本型，单引号、双引号或者不加都可以 (e.g. "foo", 'bar', baz)
*   颜色值类型 (e.g. blue, #04a3f9, rgba(255, 0, 0, 0.5))
*   布尔值 (e.g. true, false)
*   nulls (e.g. null)
*   值列表，通过空格或者逗号分割值 (e.g. 1.5em 1em 0 2em,Helvetica, Arial, sans-serif)
*   值映射，一个关键词对应一个值 (e.g. (key1: value1, key2: value2))

针对这些值都有不同的运算规则以及一些实用函数，最后两个主要用来传参等。后面会提到。

### Sass 变量的作用域

变量是最基础也最常用的东西，例如一段 CSS 中有很多相同的颜色值（例如 #050709），传统的 CSS 中，你就需要不断的去复制这个值，而在 Sass 中，你就可以先在对应文件头部（一般会有一个 var.scss 文件专门用来放置变量等）声明一个变量 `$co : #050709` 然后在后面需要使用这个颜色值的时候，就可以直接写上 `color: $co;` 就可以了。

不仅仅需要输入的字符少了，而且根据规则起变量名让人更加易懂，此外后期需要修改这一种颜色的时候，只需要修改一下变量值即可全部修改，不需要搜索替换了。

但是这里我们要讨论的是 Sass 中变量的作用域问题，即变量放置的位置先后、同名变量之间的相互影响问题。

使用下面这段代码即可进行测试：

    $width: 10px;

    .wrap{
        /*$width: 20px;*/
        .main{
            width: $width;
        }
        .sidebar{
            /*$width: 30px;*/
            width: $width;
        }
        /*$width: 20px;*/
    }

    .content{
        width: $width;
    }

    /*$width: 20px;*/
    

进行编译之后输出（无视注释掉的东西）：

    .wrap {
      /*$width: 20px;*/
      /*$width: 20px;*/
    }
    .wrap .main {
      width: 10px;
    }
    .wrap .sidebar {
      /*$width: 30px;*/
      width: 10px;
    }

    .content {
      width: 10px;
    }

    /*$width: 20px;*/
    

现在是理所当然的常用模式，在最上面设置了变量为 10px，下面引用的地方全都变成了 10px。我们现在注释掉第一行的变量，然后解开最后一行的变量声明。

结果编译报错不通过，提示 `Undefined variable` 说明变量声明必须在调用变量语句的上面，这样才能正确的调用变量。

现在我们恢复到初始状态，开始测试变量是否具有类似块级作用域的特性。把上面所有注释取消掉：

    $width: 10px;

    .wrap{
        $width: 20px;
        .main{
            width: $width;
        }
        .sidebar{
            $width: 30px;
            width: $width;
        }
        $width: 20px;
    }

    .content{
        width: $width;
    }
    

编译后得到：

    .wrap .main {
      width: 20px;
    }
    .wrap .sidebar {
      width: 30px;
    }

    .content {
      width: 20px;
    }
    

这说明，Sass 中的变量没有块级作用域特性，而是随执行随覆盖随调用。调用的前面没有变量声明，就报错，有很多变量声明，就调用在它上面离它最近的变量值。

而每次做 Sass 和 LESS 对比的时候，总要提一下变量的作用域问题，因为 LESS 支持变量的块级作用域。对此我个人觉得，不要真把这类 CSS 预处理器当成编程语言来用，本来就是很简单的东西，没必要搞的太复杂，变量在最顶端声明一下，后面全局调用就可以了。

值得注意的是，如果一个项目太大，而且是很多人共同完成，这时候命名规划不好，变量容易被覆盖掉，这样就悲剧了。所以多人合作大项目，一定要制定好规范和命名等事情。

### 引用父级的 &

对于后代选择器这类的 CSS 代码，Sass 中只需要嵌套即可，这一点非常方便，省下了重复复制父级选择器，编写和阅读代码也变得十分轻松简单。

但有时候，我们也需要在嵌套结构中引用父级选择器，这时候就可以用 `&` 符号代替父级选择器，超级实用，例如：

    .parent{
        color: green;
        .son{
            color: red;
            &:hover{
                color: blue;
            }
        }
        body.orange &{
            color: orange;
        }
    }
    

将会生成：

    .parent {
      color: green;
    }
    .parent .son {
      color: red;
    }
    .parent .son:hover {
      color: blue;
    }
    body.orange .parent {
      color: orange;
    }
    

注意 `&` 与其他选择器的空格问题，例如上面的 `:hover`。

### 让一切变成字符串 #{}

变量只能作为属性值来用，例如 `color: $color;`，那变量能不能用在选择器或者属性上面吗？例如这样：

    $name: foo;
    $attr: border;
    p.$name {
      $attr-color: blue;
    }
    

编译报错，这时候就需要 `#{}` 出场了，我们需要它来将变量值转换为字符串，这样就可以用在选择器和属性上了，例如：

    $name: foo;
    $attr: border;
    p.#{$name} {
      #{$attr}-color: blue;
    }
    

这样就可以编译出来：

    p.foo {
      border-color: blue;
    }
    

再来看下面这个例子：

    p {
      $font-size: 12px;
      $line-height: 30px;
      font: $font-size/$line-height;
    }
    

目的很明确，想要声明变量然后用在 font 属性上，设置字体大小和行间距，结果编译后傻眼了，编译成了：

    p {
      font: 0.4;
    }
    

斜杠被识别为除法运算，将两个变量进行了运算，并编译了执行结果。这样显然是不行的，不符合 font 属性缩写格式，这时候就需要 `#{}` 登场了，我们修改成：

    p {
      $font-size: 12px;
      $line-height: 30px;
      font: #{$font-size}/#{$line-height};
    }
    

就可以正常输出了。

### 神奇的四则运算

Sass 支持 加法（＋）、减法（－）、乘法（＊）、除法（／）以及取余（％），这几种运算符号作用相信大家以及比较熟悉了，但是在 Sass 中还有一些需要注意的地方：

#### Sass 中的加法

加法的用法很简单，例如：

    p {
      $width: 10px;
      $width2: 20px;
      width: $width + $width2;
    }
    

很明显的输出：

    p {
      width: 30px;
    }
    

这里需要注意的是，在 Sass 数据类型中提到了，10px 这类数值虽然带有字符单位，但是也算是数值，Sass 会进行计算并对单位进行处理，至于计算是对单位的处理，后面再说。

加法运算不仅仅是计算数字，也可以跟 JavaScript 一样，连接字符串等。例如下面：

    p {
      cursor: e + -resize;
    }
    

会输出；

    p {
      cursor: e-resize; }
    

加法连接字符串时，对于引号的合并也有一定规则，如果前面字符串带有引号，后面字符串会自动包含在引号中，如果前面没有，后面带有引号的字符串也会去掉引号：

    p:before {
      content: "Foo " + Bar;
      font-family: sans- + "serif";
    }
    

会输出：

    p:before {
      content: "Foo Bar";
      font-family: sans-serif; }
    

所以这里对于一些要求带双引号的属性就要特别注意。运算的时候，会以空格作为分割，会操作相邻的两个数值，比如：

    p {
      margin: 3px + 4px auto;
    }
    

会编译成：

    p {
      margin: 7px auto; }
    

而不会是：

    p {
      margin: "3px4pxauto";
    }
    

除此之外，如果你想在一段字符串中进行计算并输出，就需要用 `#{}` 了，例如：

    $w  : 10;
    p:before {
      content: "I ate #{5 + $w} pies!";
    }
    

将会输出：

    p:before {
      content: "I ate 15 pies!";
    }
    

否则将会原封不动的输出运算表达式。

#### Sass 中的除法

除法也有很多需要注意的地方，因为除法的运算符 `/` 在 CSS 中也有遇到，例如 font 缩写属性时候的 font-size 和 line-height 属性，就需要 `/` 来分割。所以，在这些包含 `/` 的 CSS 属性中对应位置的值，是不会参与运算的，除了下面情况下：

1.  如果两个值其中一个或两个存放在变量中或者是由函数返回的值。
2.  如果值被包裹在一对括号里面
3.  如果值被作为另一个表达式的一部分

例如：

    p {
      font: 10px/8px;             // 纯 CSS 不会运算
      $width: 1000px;
      width: $width/2;            // 使用变量，执行运算
      width: round(1.5)/2;        // 使用函数返回值，执行运算
      height: (500px/2);          // 使用括号包裹，执行运算
      margin-left: 5px + 8px/2px; // 用了加法，作为表达式的一部分，执行运算
    }
    

编译后输出：

    p {
      font: 10px/8px;
      width: 500px;
      height: 250px;
      margin-left: 9px; }
    

如果想要避免在使用变量的时候，进行了运算，在上面提到了那个例子，使用 `#{}` 来包裹变量，转换成字符串，这里不再赘述。

#### Sass 运算注意事项

1, **建议运算符之间空开一个空格。**当你试着执行下面这句代码，你会发现编译器把 `$h-` 识别成了一个变量：

        $w : 7px;
        $h : 10px;
        p:before {
            width: $h-$w;
        }
    

2, **注意运算单位。**虽然 10px 也被看做是数值型，可以进行数值运算，但是对于单位是有要求的。**单位也会参与运算。**在运算中，两个操作数单位可以一致，也可以一个数没有单位，也可以都没有，而对于不同的运算操作符，单位运算结果也不同，一般来说测试结果如下：

*   加法：都没有单位输出纯数字；一方有单位，则结果输出该单位；两方相同单位，结果输出该单位；双方单位不同，报错。
*   减法：类似加法。
*   除法：两方相同单位，结果无单位；都没有单位，结果无单位；一方有单位另一方无单位，报错。
*   乘法：两方相同单位，报错；一方有单位，结果输出该单位；两方都无单位，输出无单位。

你可以用下面代码进行测试：

        $w : 7;
        $h : 10;
        p:before {
            width: $h * $w;
        }
    

至于除法和乘法奇葩的运算规则，正如上面说的那样，单位也会参与运算。除法运算的时候，双方都有单位的时候，单位就被消掉了，得到一个数值。乘法的时候，双方都有单位，结果是单位的平方（px x px），自然也不行。

### 深入理解 @extend

@extend 这个 Sass 命令是非常实用的功能，简单的方法却极大的解决了 CSS 的代码复用问题。例如在所有的项目中都有了 .clearfix 清除浮动类，在应用这个类的时候，通常会在 HTML 需要的结构上添加这个类，但是这样做就会造成 CSS 和 HTML 的耦合，不利于后期维护。

如果只在 CSS 中去对每个结构实现 clearfix 的效果，要么把这段代码不停的复制到对应选择器中，产生大量冗余，要么就把这个结构选择器翻到上面去找 .clearfix 类，加在后面，费时费力。

而使用 Sass 之后，你只需要像下面这样：

    .post{
        @extend .clearfix;
        ...
    }
    

就会自动的把 .post 加到 .clearfix 中实现 .post 结构清除浮动。当然，这只是 @extend 最基础的用法，下面讨论一下进阶用法。

#### @extend 可扩展的选择器

在了解 @extend 得基础功能之后，我扩展了一下类似 `.icon .icon-button` 这样的类，即 `@extend .icon .icon-button;` 这样写。结果报错，现在想想确实有问题，中间有空格分隔开了，就不背识别了。结果尝试加上双引号，也无解。

所以 @extend 中只能扩展单个选择器，例如：`.icon-button`、`a:hover`、`a.user:hover`等。不过虽然每次 @extend 的是一个整体的选择器，但是你可以将多个选择器写进一条 @extend 命令中，用逗号分割，例如：`@extend .message, .important;`，表示当前结构复用 `.message` 和 `.important` 的代码。

所以你可能需要想好并规划好 CSS 可复用模块，然后再在后面 @extend 。此外需要注意的是，@extend 命令无法用在其他 @ 命令中，例如用在 @media 命令中。当你在上面定义 .clearfix 类时，在 Media Queries 代码里面就无法扩展 .clearfix 类。但是你可以在 Media Queries
 里面再定义一个 .clearfix 然后扩展一下。

#### 理解 @extend 的扩展方法

来看一下下面代码的编译结果：

    #admin .tabbar a {
      font-weight: bold;
    }
    #admin .overview .fakelink {
      @extend a;
    }
    

编译结果：

    #admin .tabbar a,
    #admin .tabbar .overview .fakelink,
    #admin .overview .tabbar .fakelink {
      font-weight: bold; }
    

看着这一长串选择器，就比较晕了，为什么会组合出这样的一批选择器？慢慢的来分析一下就知道了。首先，@extend 命令的用处是用来将当前选择器与命令扩展的选择器共用一段 CSS 代码，所以要把当前选择器与命令扩展的选择器组合放在一起，并且要考虑到大部分情况，再来分析一下编译后的结果：

`#admin .overview .fakelink` 选择器要与 `#admin .tabbar a` 共用一段 CSS 代码，而且共用的是 `a` 标签的代码。由于 `#admin` 是相同的，所以统一放在最前面，只需要组合后面选择器即可。

既然扩展 `a` 标签，就先用 `.overview .fakelink` 替换掉 `a` 组合成了 `#admin .tabbar .overview .fakelink` 选择器，由于不知道 `.tabbar` 与 `.overview` 类结构的包裹情况，所以又颠倒位置生成了 `#admin .overview .tabbar .fakelink` 这样一个选择器。这样就足够了，因为已知 `.overview .fakelink` 是为了替换 `.tabbar` 选择器的下层结构 `a`，所以可以确定 `.fakelink` 结构一定在 `.overview` 结构的下一级。

这样所有情况就遍历完成了。

#### 按需求输出的 @extend

像之前说的，使用 @extend 需要规划好可复用的类放在上面，然后再在下面 @extend 调用。这样，我们的可复用部分就会编译输出到 CSS 中，但这些代码可能是无意义的，我们并不想使其编译出现在 CSS 文件中，那么就可以使用 `%` 符号来实现。

带有 `%` 符号的选择器不会被编译输出，但是可以被 @extend 到，替换之后输出，例如：

    #context a %extreme {
      color: blue;
      font-weight: bold;
      font-size: 2em;
    }
    .notice {
      @extend %extreme;
    }
    #context .title .notice2 {
      @extend %extreme;
    }
    

会编译输出：

    #context a .notice, 
    #context a .title .notice2, 
    #context .title a .notice2 {
      color: blue;
      font-weight: bold;
      font-size: 2em;
    }
    

跟正常的 @extend 一样。

### 深入理解 @mixin

@mixin 跟 @extend 命令有点类似，也是极其实用的一个命令，也有一些需要注意的细节。

#### @mixin 与 @extend 区别

@mixin 定义的是一个片段，这个片段可以是类似变量的一段文字一条属性，也可以是一整个选择器和内容，也可以是一个选择器的一部分 CSS 代码。此外还可以传递参数，通过参数生成不同代码。它需要配合 @inclde 命令来引用这段代码，类似复制的效果。@mixin 定义的内容，不会编译输出。

@extend 就是简单的扩展，基于某个选择器，将其他类似需求的选择器挂靠上，以提高复用程度。

#### @mixin 常用示例

基础用法：

    @mixin red-text {
      color: #ff0000;
    }

    .page-title {
      @include red-text;
      padding: 4px;
      margin-top: 10px;
    }

    .page-title2 {
      @include red-text;
      padding: 4px;
      font-size: 20px;
    }
    

编译输出：

    .page-title {
      color: #ff0000;
      padding: 4px;
      margin-top: 10px;
    }

    .page-title2 {
      color: #ff0000;
      padding: 4px;
      font-size: 20px;
    }
    

传递参数：

    @mixin font($color, $fontSize: 14px) {
        color: $color;
        font-size: $fontSize;
    }
    p { @include font(blue); }
    h1 { @include font(blue, 20px); }
    h2 { @include font($fontSize: 18px,$color: red); }
    

编译输出：

    p {
      color: blue;
      font-size: 14px;
    }

    h1 {
      color: blue;
      font-size: 20px;
    }

    h2 {
      color: red;
      font-size: 18px;
    }
    

由此可见，传递参数时，参数跟声明变量一致，如果变量后跟一个值，则表示该参数是可选的，带有默认值。传参时，需要按照参数定义顺序传參，也可以传參时使用参数名和值这样的参数对来传递，这样就无关参数顺序。

#### @mixin 传递多值参数

一些 CSS3 属性不仅仅只有一个值，还可以有多个值。例如 box-shadow 属性，你可以定义一条 `0px 4px 5px #666` 来指定它的阴影效果，当然，你也可以指定 `box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;` 多条属性，来细化定义多个阴影效果。

然而当你定义下面这种 mixin 片段并传值时，报错了：

    @mixin box-shadow($shadows) {
      -moz-box-shadow: $shadows;
      -webkit-box-shadow: $shadows;
      box-shadow: $shadows;
    }

    .shadows {
      @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
    }
    

由于传递进去的多个值中间被逗号分割，Sass 判定其为两个参数，于是就报错了。解决这个问题，你需要在参数后面加上三个点，表示这个参数可能包含多条属性：

    @mixin box-shadow($shadows...) {
      -moz-box-shadow: $shadows;
      -webkit-box-shadow: $shadows;
      box-shadow: $shadows;
    }

    .shadows {
      @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
    }
    

这下正常编译出：

    .shadows {
      -moz-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
      -webkit-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
      box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
    }
    

此外，多值参数还可以用在 @include 传參的时候，分解某个变量值，例如：

    @mixin colors($text, $background, $border) {
      color: $text;
      background-color: $background;
      border-color: $border;
    }

    $values: #ff0000, #00ff00, #0000ff;
    .primary {
      @include colors($values...);
    }

    $value-map: (text: #00ff00, background: #0000ff, border: #ff0000);
    .secondary {
      @include colors($value-map...);
    }
    

编译输出：

    .primary {
      color: red;
      background-color: lime;
      border-color: blue;
    }

    .secondary {
      color: lime;
      background-color: blue;
      border-color: red;
    }
    

在 .primary 中，将 $values 分解变成三个参数传递进去。而在 .secondary 中，将映射型的变量也分解进行了传參。

这个功能对于参数很多的 @mixin 来说，就很方便了。

#### 向 @mixin 传递内容

这个功能绝对必不可少，超级实用，特别是在响应式布局中。先看一下官方的例子：

    @mixin apply-to-ie6-only {
      * html {
        @content;
      }
    }
    @include apply-to-ie6-only {
      #logo {
        background-image: url(/logo.gif);
      }
    }
    

编译后输出：

    * html #logo {
      background-image: url(/logo.gif);
    }
    

定义的 apply-to-ie6-only 是一个选择器或者一段代码片段，在其中添加了 @content 这个命令。当使用 @include 命令使用这条 mixin 的时候，将一段内容包裹了起来，这段被包裹的内容就会替换掉 @mixin 中的 @content。

简直太碉堡了！

对于 Media Queries 开发就非常方便了，我们可以定义下面这样的代码：

    @mixin apply-to-iphone5 {
        @media only screen 
        and (min-device-width : 320px) 
        and (max-device-width : 568px) { 
            @content;
        }
    }
    @include apply-to-iphone5 {
      #logo {
        background-image: url(/logo@2x.gif);
      }
    }
    

编译后输出：

    @media only screen and (min-device-width: 320px) and (max-device-width: 568px) {
      #logo {
        background-image: url(/logo@2x.gif);
      }
    }
    

这样在需要 Media Queries 的地方，很快速就插入进去了，而不需要像以前那样在专门的 Media Queries 代码区域编写代码来回跳转。

而目前也推荐这种方式，即直接在相应位置下面编写 Media Querires 代码，这样在后期维护以及代码阅读方面更加简单。

### 灵活的控制语句

Sass 中也有诸如 @if、@while 等常见的控制语句来实现一些简单的流程控制。这部分主要用于 @function 或者 @mixin 等传递参数的命令，在内部进行判断，提高灵活性。但是普通情况下，也可以应用发挥巨大能量，例如：

#### @for 循环

就是简单的循环，只能使用数字，每次固定一个步长，例如：

    @for $i from 5 through 1 {
      .item-#{$i} { width: 2em * $i; }
    }
    

简单的一个循环，输出：

    .item-5 {
      width: 10em;
    }

    .item-4 {
      width: 8em;
    }

    .item-3 {
      width: 6em;
    }

    .item-2 {
      width: 4em;
    }

    .item-1 {
      width: 2em;
    }
    

对于有规律的批量的 CSS 输出非常方便。注意 @for 命令使用时有一个 through 关键词，这个参数还可以是 to 关键词，至于两者的区别，可以自行试验。

#### @while 循环

@while 和 @for 命令是非常接近的，只不过 @while 循环可以自己定义步长，例如：

    $i: 6;
    @while $i &gt; 0 {
      .item-#{$i} { width: 2em * $i; }
      $i: $i - 2;
    }
    

编译输出：

    .item-6 {
      width: 12em; }

    .item-4 {
      width: 8em; }

    .item-2 {
      width: 4em; }
    

控制起来更加灵活一点。

#### @each 遍历循环

这个是最灵活最实用的循环命令了。你可以用它遍历一个列表或者映射型的变量，然后循环输出。例如网站常见的 icon 的 CSS 代码：

    .puma-icon {
      background-image: url('/images/puma.png'); }
    .sea-slug-icon {
      background-image: url('/images/sea-slug.png'); }
    .egret-icon {
      background-image: url('/images/egret.png'); }
    .salamander-icon {
      background-image: url('/images/salamander.png'); }
    

通常来说，我们需要复制好几遍，然后依次替换掉关键词，但是有了 each 之后，我们可以遍历关键词循环输出，代码如下：

    @each $animal in puma, sea-slug, egret, salamander {
      .#{$animal}-icon {
        background-image: url('/images/#{$animal}.png');
      }
    }
    

在上面，我们定义了一个 $animal 变量作为临时变量，然后循环 puma、sea-slug、egret、salamander 这四个值，循环体内，调用 `#{}` 将变量变成字符串插入到 CSS 选择器和需要的地方。

简单的一个循环，就搞定了大量重复的东西。当然，这只是 @each 最基础的用法，这只是一个循环变量，我们还可以增加多个循环变量来构造更加复杂的循环：

    @each $animal, $color, $cursor in (puma, black, default),
                                      (sea-slug, blue, pointer),
                                      (egret, white, move) {
      .#{$animal}-icon {
        background-image: url('/images/#{$animal}.png');
        border: 2px solid $color;
        cursor: $cursor;
      }
    }
    

上面代码表示循环三个“数组”，三个循环变量分别对应每个组中的相应参数，然后用在循环中，编译输出：

    .puma-icon {
      background-image: url('/images/puma.png');
      border: 2px solid black;
      cursor: default; }
    .sea-slug-icon {
      background-image: url('/images/sea-slug.png');
      border: 2px solid blue;
      cursor: pointer; }
    .egret-icon {
      background-image: url('/images/egret.png');
      border: 2px solid white;
      cursor: move; }
    

当然，也可以循环映射型的参数：

    @each $header, $size in (h1: 2em, h2: 1.5em, h3: 1.2em) {
      #{$header} {
        font-size: $size;
      }
    }
    

输出：

    h1 {
      font-size: 2em; }

    h2 {
      font-size: 1.5em; }

    h3 {
      font-size: 1.2em; }

## Sass 扩展资源

首要的当然是 Sass 官方文档：[http://sass-lang.com/documentation/file.SASS_REFERENCE.html](http://sass-lang.com/documentation/file.SASS_REFERENCE.html) 。了解了本文这些，下一步就应该认真阅读官方文档了，你会发现本文好多例子都是从官方那里复制或者改造过来的 ^_^

然后强烈推荐开发者 [Hugo Giraudel](http://hugogiraudel.com/) 。这家伙对 Sass 的研究和开发简直疯狂，写了大量的 Sass 有关文章，并发到了很多知名网站上。他的 [Github](https://github.com/HugoGiraudel) 上也有大量关于 Sass 的项目，[Gist](https://gist.github.com/HugoGiraudel) 上面也有好多实用的 Sass mixin function 等代码片段。

国内来说， [W3CPlus](http://www.w3cplus.com/blog/tags/302.html) 发表了很多 Sass 有关的教程，W3CPlus 站长大漠也是 Sass 的狂热支持者。

最后，如果你对如何将 Sass 应用在自己的项目中或者怎么用 Sass 开始一个新的重构项目，自荐一下本人前端重构时用的模版 [ysass](https://github.com/yujiangshui/ysass) 它目前还正在完善，如果你有好的建议，欢迎提出。