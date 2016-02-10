title: 三步将你的 React Native 项目运行在 Web 浏览器上面
tags:
  - react
id: 250
categories:
  - JavaScript
date: 2016-02-10 00:08:27
---

React Native 的出现，让前端工程师拥有了使用 JavaScript 编写原生 APP 的能力。相比之前的 Web app 来说，对于性能和用户体验提升了非常多。

但是 React Native 的代码只兼容两个平台（iOS 和 Android），并没有兼容 Web 端访问。这里是因为 Facebook 开发人员认为 Web 端天生兼容性就巨麻烦，而且平台差异性是注定存在而且也要保留的，所以 React Native 的目标是 `Learn once, write anywhere`，而不是 `Write once, run anywhere`。

<!-- more -->

然而 `Write once, run anywhere` 又是一个刚需。从产品还是用户的角度试想一下，APP 的安装成本还是很高的，如何让用户马上体验到你产品的功能再决定是否要安装？此外，尤其是重要的产品，除了 APP 客户端之外，还要有一套兜底的 Web 端以便用户在某些特殊场景下使用。React Native 可以让你写一份代码跑在两个平台，但是你却还要再写一份 Web 的一模一样的应用。就显得十分蛋疼了。

于是 React web 就出现了。

## React Web 介绍

简单的一句话描述 React Web 就是：它帮你把 React Native 的组件做了一个 Web 端的实现，并提供相关打包工具，让你可以直接打包出一份可以跑在 Web 端的代码。

## 将 React Native 应用创建一个 Web 版的几个步骤

为了重点突出转换过程，这里使用 React native init 的最简 Demo 来做实验（名字叫 Awes）。[React Web 已经把 React Native 比较复杂的 UI Explorer Demo 跑起来了](http://rawgit.com/taobaofed/react-web/master/pages/uiexplorer.html#/?_k=s1mcy8)，所以只要你的代码能跑在 iOS 或者 Android 上面，你基本不用担心有什么组件上的问题。当然如果有，可以马上提 Issue 过来，我们有一个小组在支持 React web ：）。

### 第一步：安装 React web 并进行相关配置

这一步操作主要是安装 `react-web` 包以及相关依赖，并配置 webpack 打包脚本等。

为了简化这一步操作，我们开发了命令行工具 `react-web-cli` 只需要执行两行命令即可。同时命令行工具还支持启动调试服务器、打包等功能，在后面介绍。

安装 cli 工具：

```
npm install react-web-cli -g
```

安装配置 React web 等：

```
react-web init <当前项目目录>
```

执行完成之后，会在你项目目录下面 `npm install` 相关库，并自动创建 `web/webpack.config.js` 文件，里面有一份写好的配置。此时目录结构为：

```
.
├── README.md
├── android/
├── index.android.js
├── index.ios.js
├── ios/
├── package.json
└── web/
    └── webpack.config.js
```

### 第二步：添加入口文件并进行相关配置

每个项目都需要有一个入口文件，通常用来引入调用其他组件并初始化项目，比如 `index.ios.js` 表示 iOS 平台上的该项目的入口文件。为了符合 React Native 的文件命名规范，我们创建一个 `index.web.js` 作为入口文件，并且需要在 Webpack 中指定该文件为入口文件。打开 `web/webpack.config.js` 文件，修改 `config` 变量：

```
var config = {
  paths: {
    src: path.join(ROOT_PATH, '.'),
    index: path.join(ROOT_PATH, 'index.web'),
  },
};
```

然后我们创建 `index.web.js` 文件。这个文件其实跟 `index.ios.js` 非常像，只是略有不同。主要区别在于：iOS 只需要 `AppRegistry.registerComponent('Awes', () => Awes);` 即可让 Xcode 的 Native 代码接收处理你的 JS 代码，而 Web 端是需要插入到 DOM 节点中才可以用。因此我们需要在 `index.web.js` 最下面添加如下代码：

```
AppRegistry.registerComponent('Awes', () => Awes);
if (Platform.OS == 'web') {
  var app = document.createElement('div');
  document.body.appendChild(app);
  AppRegistry.runApplication('Awes', {
    rootTag: app
  });
}
```

然后在最上面 `require` 部分需要引入 `Platform` 组件。这样配置部分就已经处理完成了，执行 `react-web start` 命令即可启动调试服务器啦！

![](http://static.zhjan.com/blog/yujiangshui/2016/02/react-web01.png)

可以随便修改试下，跟 React Native 模拟器里面的体验几乎一样。

### 第三步：测试并打包 Web 版本代码

当你修改开发完，并对 Web 端也测试好了，就可以打包发布了。`react-web-cli` 工具打包的命令是：

```
react-web bundle
```

打包完成后，文件会存放在 `web/output/` 目录下面，可以直接打开 `index.html` （如果 app 有请求操作，需要起本地服务器查看），再检查一下就可以发布了。

## 这个过程中发生了什么？

好奇的同学看到这里可能会有一些疑问，上面命令行工具的一些命令做了什么事情？为什么 React web 将 React native 代码打包出一份用在 Web 端的代码？React web 安全可靠吗，里面都是什么东西？

这里简单的介绍下 React web 的实现原理和上面步骤实际做的事情。

### React Web 将 React Native 组件做了 Web 端的实现

React 将代码与平台环境分离，多了一层，这样开发者可以在平台环境层面做一些处理，使得同样一份代码适应更多的平台环境等。

* 比如 [react-canvas](https://github.com/Flipboard/react-canvas) 按照 react 的语法书写代码，在平台环境层面做一些处理（将你 react 代码运行并用 canvas 渲染），然后实现特定目标（在移动端提高性能）。
* react native 中，一份代码能同时跑在 iOS 和 Android 上面，也是一样的道理。React Native 团队在对应平台的 Native app 上面做了一些处理，使其可以解析执行 React 语法的代码。
* 还有同构（isomorphic）的应用，服务器端使用 react + nodejs 生成 HTML，客户端使用 react 获取进行客户端相关交互和功能，也是一样的道理。

为此，[react v0.14.x 版本开始，专门分成两个库 `react` 和 `react-dom`](https://facebook.github.io/react/blog/2015/09/10/react-v0.14-rc1.html#two-packages-react-and-react-dom)，其实是把对浏览器平台的特殊处理剥离了出来，单独变成了 `react-dom` 库。

React Native 比较特殊的地方在于，组件最底层的实现是 Native 的实现，所以就不支持 `span`、`div` 等标签。而动画等，也是直接调用 Native 进行界面渲染。所以不支持 web 端，但是绝大部分组件，都是可以用 Web 技术进行模拟实现。动画可以用 CSS3、基础元素可以用同等 HTML 标签模拟、布局以及兼容性问题可以用 CSS 来处理，所以 React web 只需要把 React Native 的组件用 Web 技术重新实现一遍，借助 React 这一层，即可实现一份代码运行在多个平台上面。

举一个非常简单的例子，`Text` 组件：

* [React Native 的实现](https://github.com/facebook/react-native/blob/master/Libraries/Text/Text.js#L222) 是调用了很多 React Native 底层的代码实现的。
* 对于 Web 端，输出一行文本使用 `<span>` 标签即可，所以 [React web 的实现](https://github.com/taobaofed/react-web/blob/master/Libraries%2FText%2FText.web.js#L229) 就直接搞一个 `<span>` 标签，绑一些事件什么的就 OK 了。

在 [UI Explorer demo](http://rawgit.com/taobaofed/react-web/master/pages/uiexplorer.html) 中能跑起来的 React Native 组件，你都可以放心的用。

### Webpack 帮你切换打包目标

做出了兼容 Web 端的组件，那打包的时候岂不是要把所有要打包的组件中的 `require('react-native')` 全部更换成 `require('react-web')`？不然怎么用的我的 Web 组件打包？

强大的 Webpack 附带了 `alias` 配置项可以帮你解决这个问题：

```
  resolve: {
    alias: {
      'react-native': 'react-web',
      'ReactNativeART': 'react-art',
    },
    extensions: ['', '.js', '.jsx'],
  },
```

这样在打包时，但凡 `require('react-native')` 的地方全都用 `react-web` 包替换，而 `react-web` 的 `module.exports` 与 `react-native` 的保持一致即可让代码不替换也可以工作。

此外配合插件还可以实现另外一种引入方法，请看下面。

### 通过 Haste 方法引入组件以提高性能

Webpack 以及其他的支持 CommonJS 规范的打包工具，都会把文件中 require 的所有组件都打包在一起。对于 React Native 来说代码体积大小无关紧要，而在 mobile web 来说，就要稍微重要一些了。特别是如果你的项目只需要 `Text` 组件，但由于 `require('react-web')` 结果把所有的组件全部打包进来了，就比较伤感。

基于 Webpack 插件，还可以用另一种方式引入组件以解决这个问题，你可以叫它 `Haste` 方式。使用这种方式需要加载 Webpack 插件 `haste-resolver-webpack-plugin`，默认的 Webpack 配置已经帮你加载好了，你可以直接在组件里面这样用：

```
var Text = require('ReactText');
```

而不是以前那样：

```
var {Text} = require('react-native');
```

这样 Webpack 打包时，对于前者，只会把那一个组件内容打包进来，因此可以减小体积、提升性能。这是怎么实现的呢？

加载了插件的 Webpack 打包时，会先扫描所有组件并读取组件头部 `@providesModule` 的信息（比如 [Text 组件的信息](https://github.com/taobaofed/react-web/blob/master/Libraries%2FText%2FText.web.js#L7)），然后当其他文件中 require 了这个组件名称，就会自动定位到这个文件进行打包。同时还可以区分平台，即便是同一个名字，打包时会区分平台去打包对应的文件（根据 index.xxx.js 的命名规则确定文件）。

## 一些存在的问题

在 Web 端兼容性是个非常麻烦头疼的事情，React Web 已经尽力帮你抹平兼容性问题和代码差异，尽可能的让你减少改动就可以创建 Web 版本的应用。但受限于 Web 端的一些固有限制（比如请求跨域），不可避免的就会有一些需要你改代码的地方。

为此，可以通过 `if (Platform.OS == 'web')` 的方式判断目标平台，并针对性的做一些平台兼容性处理。同样的，也可以将 `web` 替换为 `ios` 或者 `android` 判断其他平台。

在 [React web 官方文档上面已经列出来了一些平台差异问题](https://github.com/taobaofed/react-web#fix-platform-differences)，这里就不再赘述了。

欢迎踊跃尝试，遇到问题可以随时提 Issue 哦：）

