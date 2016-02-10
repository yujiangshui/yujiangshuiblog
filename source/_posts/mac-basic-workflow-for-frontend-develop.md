title: Mac 下的基础前端开发环境搭建
tags:
  - Mac
id: 198
categories:
  - 其他
date: 2014-04-17 05:07:23
---

最近刚买了 Mac Pro，折腾了半个月，目前已经完全适应并投入使用。因为我是做前端的，所以在这里总结记录一下 Mac 系统下的基础前端开发环境搭建和必备软件，给新手一点帮助。

<!-- more -->

## 几点感悟和心得

超级强大碉堡的触摸板和手势控制，简直不需要鼠标的存在，将程序等全屏，然后手势切换，即可在小屏幕实现外接大屏幕 PC 的工作效率。所以一定要熟练掌握手势操作，我已经不再用鼠标了。

比较适合双屏幕工作，因为最新的系统，两个屏幕之间比较独立，多个程序界面切换相当方便。配合时时刷新的插件，可以快速方便的看到效果。

不过也有地方不太好，不够灵活开放，例如外接显示器也有一些要求，字体会发虚；机身会带静电，但是习惯就好。

第一次遇到这样的安装软件方法，之前配置 linux 的时候，都得编译，或者用 yum 自己安装，执行命令之类的。而 Mac 的程序安装，感觉有点奇怪。一般是挂载 dmg 镜像，然后会弹出一个窗口，让你把 app 文件拖动到 Application 文件夹中。或者直接解压出现 app 文件，双击就算是安装好了。卸载可以直接删掉 app 文件或者（最好）用 CleanMyMac 这类软件。

系统里还内置里很多很多有用的功能，你可以多探索探索，比如三指敲击触摸板会弹出单词翻译，选中英文摁下 option ＋ esc 会给你读出来，而且发音不错。当然这些功能需要你设置一下，发音的设置可以参考：[程序员如何优雅的使用 Mac](http://www.zhihu.com/question/20873070) 。

## 必备软件

*   [Alfred 2](http://www.alfredapp.com/) 必备神器，随手打开即可快速启动各种需要的程序或者文件目录等等。
*   [Pocket](http://getpocket.com/) 阅读文章、资料等必备工具。
*   [Dropbox](https://www.dropbox.com/) 同步备份盘。
*   [iTerm2](http://www.iterm2.com/) 听闻强大，还在摸索中。
*   [Moom](http://manytricks.com/moom/) 快速调整窗口的大小和位置，减少切换。
*   [LICEcap](http://www.cockos.com/licecap/) 小巧实用的 GIF 录像软件。也可以用 QuickTime 来录制视频，但是录出来 mov 实在太大。
*   [RescueTime](https://www.rescuetime.com/) 检测你日常时间都做了什么，方便记录工作情况，还可以设置工作目标等。
*   [Scroll Reverser](https://pilotmoon.com/scrollreverser/) 小工具解决大麻烦，可以设置让你的鼠标正常方向滚动，让触摸板还是原来的滚动方向，对于用 PC 鼠标的比较方便。
*   [CleanMyMac](http://macpaw.com/cleanmymac) 清理系统和卸载程序用的。
*   [Vox](http://coppertino.com/) 我的无损音乐就靠它播放了。
*   [Mou](http://mouapp.com/) Markdown 语法编辑器，小巧而美，写写文章就用它。
*   [The Unarchiver](http://wakaba.c3.cx/s/apps/unarchiver) 解压打包 Zip。

其他的诸如 迅雷、百度同步盘、有道云笔记、QQ、阿里旺旺 等等软件，就按照你自己的需求来安装就好，mac 上都有。

此外，建议尽量不要通过官方的 App store 安装程序，速度太坑了。例如 xcode 这种庞然大物，使用迅雷的 Thunder Store 搜索下载，速度超快，毕竟是专业下载的。

## 前端开发环境搭建

### 开发工具

#### Sublime text

第一位总是要献给神器 Sublime text，最爱，不解释。下载下来之后，各种安装插件配置，跟 Win 区别不大，就是一些操作的快捷键有些变化。另一个就是默认用 Sublime text 打开文件的时候，会弹出一个新窗口打开，而不是一个新标签，面对如此反人类的操作，需要设置一下：

打开 Sublime text 然后找到 “Preferences” 下面的 “Setting－User” 选项，打开在里面添加如下参数：

    "open_files_in_new_window": false,
    

即可。一般还会加上：

    "word_separators": "./\\()\"':,.;&lt;&gt;~!@#$%^&amp;*|+=[]{}`~?"

这样双击的时候，就可以把“带有中横线的类名”一块选中了。

除此之外，唯一感觉不太好的就是 Sublime text 代码区域最下面没有 Win 平台下面的空白了，最后一行代码位于最下面，这样编辑的时候不得不在下面回车好几行使最后一部分不要太靠下。不知道有没有对应设置可以调整一下。如果你知道还望告知。

#### Brackets

Adobe 公司出品的专为前端开发做的免费开源编辑器，附带 实时预览 等等前端常用的功能，还在不断的快速开发之中，功能已经比较强大了，也有很多插件，有兴趣也可以多尝试一下。

简单用了一下，只能说界面和交互真心不错。

#### Node.js 等等

Mac 的 OS X 系统基于 Unix，带有终端执行命令行命令，对于 Node.js 这类的工具，支持性和实用性完爆 Window，了解有些简单命令，有助于提高你的效率。

#### 本地搭建 AMP 环境

Mac 已经内置了 Apache 和 PHP 环境，你只需要开启和配置一下即可，至于 MySQL 就需要自己安装了。

具体操作不再赘述，详情请看：[OS X Mountain Lion 系统配置 Apache+Mysql+PHP 详细教程](http://www.guomii.com/posts/30136) 。

中间还是会遇到一些坑的，如果之前没有配置过 Linux 服务器，可能会比较吃力。但是不吃力怎么能进步？

#### 浏览器们

Chrome 、Chrome Canary、Firefox、Safari 这些家伙们肯定少不了，漏了 IE ？一会就把它找过来。

### 前端辅助工具

#### 马克鳗尺寸测量工具

[Mark Man](http://www.getmarkman.com/) 这个工具真是相见恨晚，帮你标注 PSD 或者设计稿图片的尺寸、颜色，还可以写注释。而且基于 Adobe 的 AIR ，跨平台兼容。Mac 配合触摸板也超级方便，对 @2x 文件名的图片尺寸还会自动减半，好用我的都快流泪了，我之前都是用 QQ 截图量尺寸 T_T

#### PhotoShop

必备，正版授权要是 300 或许会考虑买个正版的，但是 3K 有点难承受。

Mac 上的 Photoshop 超好用，跟 Win 的没什么区别，本来以为用触摸板操作会比较蛋碎，完全不是这样。

#### Charles

Mac 下的抓包工具，类似 fiddler ，更多解决方案推荐：[如何在 Mac 下使用 Fiddler](https://www.imququ.com/post/user-fiddler-on-macos.html)。

#### Koala

[Koala](http://koala-app.com/index-zh.html) 主要用来编译 Sass 文件等等，感觉要比 codekit 好用的多，用我自己总结的 [ysass](https://github.com/yujiangshui/ysass) 重构模版，直接拖动进去监听自动编译输出，再配合 browser-sync 工具自动同步刷新，一气呵成。

#### Dash

[Dash](http://kapeli.com/dash) 是开发文档集合应用。安装常用文档之后，遇到什么 API 或者 CSS 属性忘记了，直接在里面搜索即可。与传统的使用 google 搜索相比，用这个定位更加快速，文档也采用比较权威的官方文档，比较精准，此外也附带了 google 搜索结果。

有了它，查询文档效率和准确度至少提升 30%。

### 前端测试工具

#### BrowserSync

[BrowserSync](http://browsersync.io/) 是必备的前端测试工具，使用它可以跨平台的同步测试。安装之后，通过命令行监听项目目录，会自动建立一个本地服务器，根据你局域网的 IP 加上端口生成 URL，当文件产生变动，它会自动刷新到浏览器中，实现类似 livereload 的功能。

当然，更主要的是它的同步测试功能，它生成的局域网 URL 地址，只要设备位于同一个局域网内，都可以访问。这样，利用电脑做 Wifi 或者设备都连接到无线路由器上，就可以测试本地的项目页面。于是你就可以开着虚拟机、iPhone 手机、Android 手机以及平板设备和别的同学的电脑，同时测试看到具体的效果，而且修改一次，自动刷新所有设备浏览器。

至于同步，简单的说，你的操作也会同步到其他设备上，比如你在手机上滚动页面，它会在电脑上也同步滚动桌面版的页面等等。

具体的用法，我还会单独写文章介绍，这里不再赘述。

#### Parallels Destop 虚拟机

一开始我用的还是 Win 下面用的免费虚拟机 virtualbox，跟 Win 下面一样，安装系统等等。但是发现网络连不上，局域网都连不上，这还测试个毛线。

于是看到很多推荐 Parallels 虚拟机的，就安装试了一下，瞬间被强大的功能惊到了，居然还有一种模式，让你直接打开某个 windows 下面的应用程序，仿佛就是 Mac 本地应用一样。网络和与宿主的文件传递等，就更不用说了，解决的相当漂亮。总之不多赘述，具体还是留给你自己体验体验吧。

一般装个 XP 系统和 Win7 系统就可以，Win7 我用的精简版的 Tiny 7，用这个比较精简节约空间和资源，毕竟只需要用它的 IE。

## 更多资源

本人目前安装配置的差不多就是这样子，网上还有更多关于 Mac 的使用技巧和应用推荐，后面我也会继续分享一些与前端开发有关的技巧。推荐阅读：

*   [程序员如何优雅地使用 Mac？](http://www.zhihu.com/question/20873070)
*   [如何更优雅地使用 OS X？](http://www.zhihu.com/question/20520972)
*   [Best-App](https://github.com/hzlzh/Best-App)