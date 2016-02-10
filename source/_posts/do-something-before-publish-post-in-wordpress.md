title: WordPress 中如何在发布文章前对文章内容进行预处理
tags:
  - php
id: 121
categories:
  - WordPress
date: 2014-01-22 01:32:46
---

我们在 WordPress 发表文章的时候，有时会遇到一些特殊的需求，比如把文章中的链接变成可点击，或者过滤掉文章内容 HTML 标签中的某种属性等。

我近期的项目中就遇到了后面的这个需求，因为直接粘贴复制网络上的文章，往往会夹带着 HTML 的 class、id 和 style 属性值，这些无用的内容会潜在的影响正文的排版布局和样式，所以要过滤掉。

<!-- more -->

## 解决思路

如果想要过滤掉发布文章时，文章内容中 HTML 标签中的 class、id 和 style 等属性，首先需要在摁下“发表”按钮的时候，使用 PHP 正则匹配对要发表的文章内容进行正则匹配处理，替换掉无用的内容，最后继续执行插入数据库的操作。所以这个问题就分成了几个小步骤：

1.  “获取”文章内容，传递给处理函数
2.  处理函数使用正则匹配对文章内容进行处理
3.  将处理好的内容返回，让 WordPress 把内容插入数据库

## 解决方案

面对第一个步骤，WordPress 有一个很强大的“钩子”（hook）开发机制，实现各种功能和开发插件必不可少的功能。简单的说，就是 WordPress 在执行某些关键性的操作时（例如发表文章、发表评论、修改文章、删除文章、新增用户等等），会插入一个“钩子”，这样你就可以在`functions.php` 中或者插件中，使用 `add_action` 或者 `add_filter` 函数挂上这个“钩子”，并增加自定义的函数对数据进行一个动作或者进行过滤。

例如在发表文章的时候，在提交到插入数据库之前，会有一个叫做 `wp_insert_post_data` “钩子”，如果你想对文章进行过滤处理，你就需要在 `functions.php` 文件中，新建一个处理函数，然后将对应处理函数绑定到这个钩子上面。

首先，你需要找到你要用的“钩子”，你需要浏览 WordPress 官方的 [Filter Reference](http://codex.wordpress.org/Plugin_API/Filter_Reference) 和 [Action Reference](http://codex.wordpress.org/Plugin_API/Action_Reference) 文档，里面是长长的“钩子”列表，看一下下面的说明，然后找到对应的“钩子”就可以开始使用了。比较常用的“钩子”已经给出官方文档和使用说明了，例如：[`wp_insert_post_data`](http://codex.wordpress.org/Plugin_API/Filter_Reference/wp_insert_post_data)，不过由于列表太长了，大部分的没有给出很详细的使用说明，就需要你按照经验来使用。

找到需要的“钩子”之后，使用 `add_filter` 函数（具体用法可以看一下官方文档：[add filter](http://codex.wordpress.org/Function_Reference/add_filter)）将钩子和处理函数进行挂钩函数用法如下：

    add_filter($tag,  $function_to_add,  $priority = 10,  $accepted_args = 1);

就本例而言，基本结构如下：

    <?php
    function wpjam_insert_post_data( $data , $postarr ) {
      // 处理函数的逻辑部分和功能代码
      return $data;
    }

    add_filter( 'wp_insert_post_data', 'wpjam_insert_post_data', '99', 2 );
    ?>
    
这样，我们的第一个步骤就完成了。下面来编写函数的处理代码。既然要过滤文章中的具有某些特征的代码，所以需要使用 PHP 的正则匹配替换掉。可以使用如下的 PHP 代码：

    $date = preg_replace('/<([a-z]+?)\s+?.*?>/i', '<$1>', $date);
    
根据 [`wp_insert_post_data`](http://codex.wordpress.org/Plugin_API/Filter_Reference/wp_insert_post_data) 文档可以看出，传递进去的 `$data` 数组里面是文章的相关信息，我们需要处理的是正文内容，所以使用 `$date['post_content']` 和`$date['post_content_filtered']` 这两个变量，那么就可以写出下面这段代码：

    function wpjam_insert_post_data( $data , $postarr ) {

        $date['post_content_filtered'] = preg_replace('/<([a-z]+?)\s+?.*?>/i', '<$1>', $date['post_content_filtered']);
        $date['post_content'] = preg_replace('/<([a-z]+?)\s+?.*?>/i', '<$1>', $date['post_content']);

      return $data;
    }

    add_filter( 'wp_insert_post_data', 'wpjam_insert_post_data', '99', 2 );

因为函数里面已经将处理后的数据 `return` 了，这样第二步和第三步就完成了，我们的这个需求也就实现了。

## 总结

正是因为有了这种开发机制，WordPress 的灵活性和扩展性大大增强。如果你还想对文章进行其他处理（例如文章末尾加版权信息等），都可以继续编写函数，挂钩在对应的钩子即可。