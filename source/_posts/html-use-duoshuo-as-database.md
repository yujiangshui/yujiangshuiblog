title: 将多说作为静态页面的数据库
date: 2015-02-06 23:04:42
id: 241
categories:
  - 其他
tags:
 - duoshuo
---

前几天想做一个[测试](http://yujiangshui.com/webview-core-test/)，思路就是获取 UA 并保存然后分析数据。就实现 UA 获取功能做一个 HTML 页面使用 JavaScript 就可以实现了，这样正好可以托管到 Github Pages 上，连服务器都省下了。但实现一个数据存放统计功能，单纯获取信息是不行的，我们还需要一个数据库来存放数据，然后读取分析。

那么问题来了，如何在静态页面上来保存信息、存储数据？

方法肯定是 AJAX 发送到第三方服务，由于最近在看 Github 的 API 文档，所以考虑在对应 Repo 上创建 Issue 来实现。但是这类 POST 操作，肯定是需要各种权限校验的，Github 当然不例外，实在是太麻烦了，所以寻找其他替代品。Issue 带有评论功能，于是就想到了多说。

看了一下多说 API 的 [发表评论文档](http://dev.duoshuo.com/docs/512d6e2e418847315a000001) 和 [获取评论文档](http://dev.duoshuo.com/docs/54460b82f42e54ec03e50082)，太棒了，正好具备我们需要的功能。可以通过 AJAX 发表评论，评论的内容就是我们需要记录的数据，我们也可以将评论数据拉去下来进行分析。它的权限校验也没有很复杂，只需要发送对应的 Secret ID 即可。

下面就看看具体如何实现。

## 使用多说存储、分析数据

### 配置多说站点

首先你需要创建一个新站点，无论你之前有没有用过他们的服务，你都应该为测试项目创建一个单独的站点。因为需要使用 Secret ID，并且会暴露在前端被别人看到。

打开[多说首页](http://duoshuo.com/)，点击“我要安装”，会跳转到创建站点界面，填写完成即可创建站点。

之后我们需要创建一篇文章，因为评论是要挂钩到对应文章上面的。在多说后台，找到“文章”标签，手动添加一篇文章，记下关键信息 `Thread_Key` 即可，其他的随便填下就可以。


### 浏览器端发送数据

怎么获取你要保存的数据这里不再赘述，需要注意的就是考虑好数据存储格式，比如是 JSON 字符串等，设计好键值，到时候方便写程序做数据统计和分析。

根据这个 [多说发表评论 API](http://dev.duoshuo.com/docs/512d6e2e418847315a000001)， 我们在浏览器端就是写一个 AJAX 去发送 POST，但是这里面有个跨域提交 POST 的问题，这里我使用 `iframe` 来解决跨域问题。

`iframe` 跨域其实不能算是 AJAX，它的大体思路就是创建一个 `form` 表单，`method` 设置成 `POST`，`action` 设置成目标 API 地址，将这个表单提交。因为表单提交会跳转，所以在外面包裹一层 `iframe`，这样不会影响当前页面，伪造出一种我用过 AJAX 的感觉。

[David](http://www.d-mueller.de/blog/) 在 [Cross Domain AJAX Guide](http://www.d-mueller.de/blog/cross-domain-ajax-guide/) 提供了一个很棒的函数：

```
function postIframe(target_url, method, params){
    //Add iframe
    var iframe = document.createElement("iframe");
    document.body.appendChild(iframe);
    iframe.style.display = "none";
    
    //Give the frame a name
    var frame_name = "frame_name" + (new Date).getTime();
    iframe.contentWindow.name = frame_name;
	
    //build the form
    var form = document.createElement("form");
    form.target = frame_name;
    form.action = target_url;
    form.method = method;
	
    //loop through all parameters
    for (var key in params)
    {
        if (params.hasOwnProperty(key))
        {
            var input = document.createElement("input");
            input.type = "hidden";
            input.name = key;
            input.value = params[key];
            form.appendChild(input);
        }
    }

    document.body.appendChild(form);
    form.submit();
}
```

使用方法很简单，你把要发送的数据保存成 JavaScript object 格式，往里面一丢即可：

```
var obj = { my_request_is: "foo", bar: "baz" };
postIframe("多说 API URL", "POST", obj);
```

这里要发送的 obj 需要添加一些必要的参数让多说校验，这时候[文档](http://dev.duoshuo.com/docs/512d6e2e418847315a000001)的坑就来了。

`short_name` 中所说的多说二级域名，并不是 `jiangshui-test.duoshuo.com`，而只是前面部分 `jiangshui-test`，按常理理解二级域名一般是整个域名。后面多说其他文档有详细提到这一点，但是这里没有。

在请求参数一节，直观的看，必须提交三个参数：`short_name`、`secret`、`message`，但实际上不填写 `thread_key` 或 `thread_id` 以及 `author_name`、`author_email` 是有前提的，在多说获取到相关信息的情况下可以不写，否则会报错。其中 `thread_key` 或 `thread_id` 填写一个即可，`author_email` 参数如果你在多说后台 “设置” -》“基本设置” 中，勾选掉 “要求游客输入邮件地址” 之后，也可以不发送。

那么你的发送代码差不多这样子：

```
var result = {};
result.short_name = 'jiangshui-test';
result.thread_key = '1';
result.secret = '4ead768a9e5d58******************';
result.author_name = 'testname';
result.message = JSON.stringify(dataObj);
	
postIframe('http://api.duoshuo.com/posts/create.json', 'POST', result);
```

### 拉取数据进行分析

要开始分析数据了，可以通过接口拉取下来，官方后台的工具有导出功能，但导出的不仅仅是评论内容还包括 ID 各种数据。这里我只需要评论内容即可，简单的用 Nodejs 写了个拉取工具 [pull-duoshuo-data](https://github.com/yujiangshui/pull-duoshuo-data)，Nodejs 处理 JSON 非常方便。

此外，在发表评论的时候，多说也会记录一些 UA 等信息，不一定仅仅拉取评论，你可以根据 [获取文章评论](http://dev.duoshuo.com/docs/54460b82f42e54ec03e50082) 文档获取更多信息。

## 基于多说开发更多玩意

多说最基础的使用就是做评论、显示评论，本文提供了一个作为临时“数据库”的用法，除此之外还有更多的用法。

### 使用多说做博客（Airpub）

郭宇开发的 [Airpub](http://airpub.io/) 是纯前端，基于多说的博客系统。虽然需要发布文章等，但是并不需要 Secret ID 等。简单的看了一下代码，发现开发思路很赞。顺带着咨询多说小武，也了解了更多关于多说开发的细节。

多说会在页面暴露一个名为 `DUOSHUO` 的全局变量，里面提供了一些方法和当前登录用户信息等。你可以使用这个变量调用更多功能以及获取信息。但是需要注意，这个全局变量的有关说明并没有在官方文档中，因为它没有制定标准规范，可能随时有变化，**慎用**！

使用多说接口发表文章或者评论的时候，多说服务器会根据已登录用户的信息来鉴权。如果你登录了，发表一个评论，虽然你在 POST 接口的时候指定了 `author_name` 也不将该评论的作者修改成这个指定的名字，而是直接使用登陆后的用户信息。此外，站点的管理员权限的用户，具有向多说同步文章（实现发表文章功能）的权限。

那么 Airpub 的大体思路就是：文章、页面、评论展示当然就是 Get，没有什么问题；拉取站点信息与已登录用户信息（DUOSHUO 里面获取）对比，发现是管理员则显示编辑按钮以及创建文章，发送的时候直接 POST 接口即可，多说服务器会自动鉴权，这样也就不需要 Secret ID 了。

此外，还可以调用显示最近访问某文章的用户（例如[这篇文章](http://blog.wpjam.com/m/js-sdk-4-tips/)下面），为博客增强了社交属性。当然 Airpub 最大的局限就是 SEO 了，同时也不太符合普通博客的操作习惯，难以大面积推广应用。

这种场景要有用户已经登录的前提，如果不需要用户登录就可以进行评论发表，还是需要 Secret ID 的，如果不想暴露，也可以用后端做一个数据转发。

### 使用多说做论坛

多说都能做博客了，轻量级的论坛当然也是 OK 的，不过这里需要后端的支持。例如 Denis 随手作的一个轻量级论坛 <http://bbs.wpjam.com/>。

最后，特别感谢 @多说小武 帮忙 Review，提供更多开发思路。

