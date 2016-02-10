title: 检测通过 JavaScript 动态加载的资源是否完成加载
date: 2015-01-06 16:09:38
tags:
id: 238
---

书签栏工具是特别有意思的东西，比插件轻量，比手工敲到 DevTools Console 里面方便，特别实用。比如之前在学校系统上计算自己的学分，别的同学手工计算三年多的考试成绩，其实写一段 JS 一循环就出来了。于是我就做成了书签栏工具，分享给大家，点击一下就计算出来了。

因为复杂点的书签栏工具代码往往比较多，同时为了方便更新版本，往往通过加载外链的方式，于是就搞了个 [bookmark-tool-loader](https://github.com/yujiangshui/bookmark-tool-loader) 书签栏加载函数。由于一些 JS 有依赖关系，或者需要在调用之后初始化一下。所以就有了这种检测通过 JS 动态加载的资源是否完成加载的需求，当 load 完成之后，我们就可以继续加载新脚本，或者调用函数进行初始化了。

## 检测动态加载资源是否完成加载的方法

在资源加载完成的时候，会抛出一个 `load` 事件。所以我们可以监听 `load` 事件来判断该资源已经正确加载了。例如：

```
  var url = "CSS URL",
      head = document.getElementsByTagName('head')[0];
      link = document.createElement('link');

  link.type = "text/css";
  link.rel = "stylesheet"
  link.href = url;

  head.appendChild(link);

  link.onload = function () {
    // 加载完成
  }
```

但这种方法在 IE 中检测某类资源时会有兼容性问题。经过测试 IE 浏览器下需要监听 `onreadystatechange` 状态变化，根据状态变化来判断是否加载完成：

```
link.onreadystatechange = function() {
	if ( link.readyState === 'loaded' || link.readyState === 'complete' ) {
		handle();
	}
};
```
对此，我对常用资源做了一下测试。

## 测试

考虑到常用的资源有 JS、CSS、IMG，所以本文就拿这三种资源来做测试。测试关键代码如下：

```
  // 监听资源加载
  // #1
  link.onload = function () {
    output('onload 【有效】');
  }
  // #2
  link.addEventListener('load', function() {
    output("addEventListener 监听 load 事件【有效】");
  }, false);
  // #3
  link.onreadystatechange = function() {
    var state = link.readyState;
    if (state === 'loaded' || state === 'complete') {
      link.onreadystatechange = null;
      output("onreadystatechange 监听加载【有效】");
    }
  };
```
主要测试 `load` 事件和 `onreadystatechange` 方法，测试 CSS 为 <http://jiangshui.b0.upaiyun.com/com/blog/loader/load-test2.css>，JS 为 <http://jiangshui.b0.upaiyun.com/com/blog/loader/load-test.js>，图片随便选了一张。主要测试 Win 下 IE6、IE6+ 以及 Mac 的 Safari、Chrome。

### 测试 JS

测试文件 [load-js.html](/demo/loader-test/load-js.html)，测试 JS 的加载，当 JS 加载完成之后，会在 console 打出 `loaded`。

测试结果如下：

#### IE6

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/jsie6.png)

#### 其他版本 IE

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/jsothie.png)

#### Chrome

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/jschrome.png)

#### Safari

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/jssafari.png)

IE 系列浏览器还是比较奇葩的，不触发 JS 资源加载完成之后的 `load` 事件。

### 测试 CSS

测试文件 [load-css.html](/demo/loader-test/load-css.html)。当 CSS 加载完成之后，会绿瞎双眼。

测试结果如下：

#### IE6

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/cssie6.png)

#### 其他版本 IE

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/cssothie.png)

#### Chrome

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/csschrome.png)

#### Safari

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/csssafari.png)

在 IE 中动态加载 CSS 居然支持 `load` 事件，由于 `addEventListener` 是标准的监听函数，早期 IE 本身就不支持他，所以也没显示出来。

### 测试 IMG

测试文件 [load-img.html](/demo/loader-test/load-img.html)，当 image 加载完成之后，会出现我的头像。

测试结果如下：

#### IE6

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/imgie6.png)

#### 其他版本 IE

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/imgothie.png)

#### Chrome

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/imgchrome.png)

#### Safari

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/imgsafari.png)

IE 也支持 `load` 事件，同时也支持 `onreadystatechange` 的方法。

## 结论

通过测试结果即可看出，IE 浏览器对动态加载的资源，除了 JS 之外，都触发 `load` 事件，无论什么资源都可以通过检测 `onreadystatechange` 状态来实现。其他标准浏览器，则触发 `load` 事件。那么在检测的时候，就可以这样写：

```
if ( type === 'js' && document.all ) {
	res.onreadystatechange = function() {
		if ( res.readyState === 'loaded' || res.readyState === 'complete' ) {
			// res loaded, do something
		}
	};
} else {
	res.onload = function() {
		// res loaded, do something
	};
}
```

判断如果是 JS 类型同时是 IE 浏览器的花，则用 `onreadystatechange` 的方法，否则全都用 `load` 事件来触发加载完后的回调函数。