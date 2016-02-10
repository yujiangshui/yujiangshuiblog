title: 搭建 LAMP 服务器的一些问题和技巧
tags:
  - centos
id: 69
categories:
  - Linux
date: 2013-11-30 13:18:57
---

新博开张，虽然想写几篇长篇大论的总结性技术文章，但是需要的时间和精力比较多。前几天主要折腾的是目前博客所在的 VPS，那么就来总结一下遇到的问题和一些技巧。如果你是新手，或许可以找到一些你遇到的问题。

<!-- more -->

## 在 Putty 中保存 Shell 界面的样式

[PuTTY](http://www.putty.org/) 是非常简洁好用的 SSH 连接软件，使用这个软件可以连接到远程的 Linux 服务器，然后执行命令来控制服务器。但是它默认的样式实在是太难看了，字体很小而且是有衬线的：

![默认的 PuTTY shell 样式](http://jiangshui.b0.upaiyun.com/blog/2013/11/linux0.png)

这显然已经超出了我的审美容忍程度，于是要设置一下，至少大一点变成无衬线的比较好。在打开 PuTTY 的时候或者进入 Shell 界面点击左上角都会出现一个菜单，点击 Change Setting 就可以打开下面的界面：

![修改 PuTTY 的 shell 样式](http://jiangshui.b0.upaiyun.com/blog/2013/11/linux2.png)

按照自己的审美观来设置就好，但是设置好的样式，只能在本次操作中应用，不要关闭这个窗口，点击一下 Session 然后找到你的服务器 IP ，再点击一下 Save 就可以保存起来，以后每次登陆就直接应用这个样式。在不知道这个技巧之前，我可是每次登陆都要设置一下 T T 。

![我的 PuTTY 的 shell 样式](http://jiangshui.b0.upaiyun.com/blog/2013/11/linux1.png)

虽然只是改了一下字体和大小，但好看多了，用起来也更爽了。

## 新手使用 yum 来安装服务器软件

我当然是新手，在此之前，只接触过桌面版的 Ubuntu ，买了 VPS 之后，安装的是 CentOS 于是两星期前刚买了一本书，看了一个星期就开始上手配置。为了更好的“配置”和了解 Linux 下的安装，采用的是源代码编译。然后第一次编译配置参数有问题，重新编译的时候，连 `make clean` 都没执行，结果参数一直有错，然后我就因为这样重装了。

第二次安装有了经验，折腾了两天安装好了 LAMP 环境，并且可以上线访问。但是服务器变得很卡，甚至打不开网站，用 `top` 命令看了一下服务器资源消耗，也没看出什么问题。于是就感觉是这个 VPS 服务商的问题。

幸亏问了一下 [@Summer](http://www.84tt.com/) 然后他告诉我可以重装然后单独安装 Apache 然后再进行网速测试，同时尽量使用 yum 这类的方式来安装。于是我花了半天时间 ( 详情参见[这篇文章](http://lovesoo.org/centos-6-3-yum-install-lamp-apache-mysql-php.html) )，使用 yum 重装了服务器，然后一切恢复正常。而且常用的模块都安装上了，文件都给配置好了，真是太方便了。

当然，编译之类的还是要学习的，不过还是在本地的虚拟机里面折腾吧，正式上线的网站是不能随便挂掉的。

## 在 Apache 中添加多个站点

一台 VPS 肯定要放多个站点。首先你需要修改 DNS 设置，将域名指向你的服务器 IP。然后在 Apache 中，添加新站点需要编辑 httpd.conf 这个配置文件。在这个文件的最下面会找到类似这样的模板：

    #<VirtualHost *:80>
    # ServerAdmin webmaster@dummy-host.example.com
    # DocumentRoot /www/docs/dummy-host.example.com
    # ServerName dummy-host.example.com
    # ErrorLog logs/dummy-host.example.com-error_log
    # CustomLog logs/dummy-host.example.com-access_log common
    #</VirtualHost>
    

 里面的参数很容易就能明白，ServerAdmin 填写你的邮箱，DocumentRoot 填写这个站点对应的文件夹，ServerName 填写要接收的域名，后面两个就是 Apache 的访问日志和错误日志。填写完成之后，去掉前面的 # 号，然后重启 Apache 服务器。然后出问题了。

 在没有虚拟主机之前，Apache 的配置文件中，有一个默认的主机配置。如果你在下面直接新建了一个虚拟主机，默认的设置下，就会与主域名发生端口冲突，这时候，你需要找到配置文件中的 `#NameVirtualHost *:80` 然后将前面的 # 号去掉才行。

 这样就配置好了，但是重启之后，又遇到了问题。如果在 ServerName 的参数里面，你填写的是 yujiangshui.com ，当你访问 www.yujiangshui.com 时，并不会调用对应目录的文件，而是读取了主域名下的文件。毕竟这是两个不同的域名，而配置文件中只设置了接收其中一个域名，那另一个肯定指向了当前服务器的 IP 地址，于是就读取了主域名下的文件。这时候，你需要在 ServerName 配置下面添加一个新参数：

    ServerAlias *.yujiangshui.com
    

这一行没有在模板中，比较坑爹啊，它的意思就是将 yuajignshui.com 的所有二级域名，也指向这个虚拟主机。这样就 OK 了。

## 无需重新编译增加 PHP 模块

这是在第一次使用源代码编译安装 PHP 环境的时候遇到的坑。

在 Apache 中，以模块的方式来实现 PHP 的功能。如果你需要 Apache 实现 PHP 的 mbstring 功能，那么你需要在 PHP 源代码编译的时候，加上对应的参数来同时编译 mbstring 模块。如果你当时忘了加参数，又想要增加这个模块，除非重新编译 PHP 源代码并且加上参数，或者使用 phpize 来编译模块。具体操作如下：

    # cd extname
    # phpize
    # ./configure
    # make
    # make install
    #上边的步骤将生成：extname.so文件
    修改php.ini：
    extension=extname.so
    

简单的说，先 cd 到 PHP 源代码包中对应的模块目录下面，然后执行 phpize 程序，然后就是常规的编译方法。这样就单独编译出这个模块文件，再把它复制到已经编译好的 PHP 对应的 module 文件夹下面，再在 php.ini 中找到增加扩展的地方加上即可。

phpize 是一个程序，它位于已经编译好的 PHP 的 bin 文件夹下面，假设你的 PHP 安装到了 `/usr/local/php5/` 当你 cd 到了某个目录下面，你需要执行 `/usr/local/php5/bin/phpize` 命令，而不能只执行 phpize 。

## Apache 开启 rewrite

首先必须要有这个模块，然后找到 Apache 的 httpd.conf 配置文件，然后找到 `LoadModule rewrite_module modules/mod_rewrite.so` 这句，去掉前面的 # 号表示应用这个。然后你还需要继续找到 `AllowOverride controls what directives may be placed in .htaccess files.` 这段注释，在下面默认的参数为 `AllowOverride None` ，把它改成 `AllowOverride All` 。这样 Apache 才会寻找并应用根目录下面的 .htaccess 文件，应用里面的 rewrite 规则。

我在配置的时候，只实现了第一步，漏了第二步，结果老是无效。

## 学会分析 Apache 日志

之前日志给我的印象就是一大坨乱七八糟的东西。但是这次真正来阅读日志的时候，才发现日志有多么重要。

还记得上面设置虚拟主机的时候，有个参数是来配置日志存放位置的吗？我们可以把这些日志下载下来看一下。

access 日志是 Apache 的访问日志，记录了每个 IP 对 Apache 资源的请求。在这里，你会发现很多 IP 地址在刺探一下敏感的目录和文件，企图发现一些漏洞来自动攻击你的网站，真的很恐怖。

error 日志是 Apache 产生错误时产生的日志，对于排查问题来说非常有用。

关于分析日志的方法 露兜博客 已经写过一篇文章 [学会分析网站原始日志](http://www.ludou.org/learning-how-to-analyse-raw-access-log.html) 这里就不再赘述。

## 开启 gzip

前端性能问题，你懂的，而且配置起来超简单，效果好。

首先要有对应模块的支持，查看一下 Apache 的 httpd.conf 配置文件中是否存在对应模块：

    LoadModule headers_module modules/mod_headers.so
    LoadModule deflate_module modules/mod_deflate.so
    

然后你可以通过 .htaccess 来设置，也可以直接在 httpd.conf 中设置。不过这里我推荐使用 .htaccess 文件来配置，因为这样可以更灵活方便，直接 FTP 上传下载修改，而且不需要重启 Apache 。而在 httpd.conf 中修改，万一有地方改错了，服务器就出问题了。开启模块之后，在 .htaccess 中添加下面语句即可：

    <IfModule mod_deflate.c>
    SetOutputFilter DEFLATE
    SetEnvIfNoCase Request_URI .(?:gif|jpe?g|png)$ no-gzip dont-vary
    SetEnvIfNoCase Request_URI .(?:exe|t?gz|zip|bz2|sit|rar)$ no-gzip dont-vary
    SetEnvIfNoCase Request_URI .(?:pdf|mov|avi|mp3|mp4|rm)$ no-gzip dont-vary

    AddOutputFilterByType DEFLATE text/*
    AddOutputFilterByType DEFLATE application/ms* application/vnd* application/postscript application/javascript application/x-javascript
    AddOutputFilterByType DEFLATE application/x-httpd-php application/x-httpd-fastphp

    BrowserMatch ^Mozilla/4 gzip-only-text/html
    BrowserMatch ^Mozilla/4.0[678] no-gzip
    BrowserMatch \bMSIE !no-gzip !gzip-only-text/html
    </IfModule>
    

当然，这是个大概的写法，你可以根据自己的压缩需求来配置这个参数。

## Apache 屏蔽 IP 地址

在前面我分析了一下 access 日志之后，发现了很多恶意的 IP 地址，这时候，就可以用 Apache 来屏蔽这些 IP 的访问，当它们请求的时候拒绝服务。这样可以节省一些资源，同时减少安全隐患。

首先 Apache 需要支持 .htaccess ，然后编辑要屏蔽的 IP，这里有好几种方式可以灵活的使用：

    <Limit GET HEAD POST>
    order allow,deny
    deny from 110.85.104.152
    deny from 110.85.113
    deny from 110.85.113.0/24
    deny from 110.87
    deny from 110.87.0.0/16
    deny from 110.86.167.210 110.86.184.181
    deny from 110.86.185.0/24 110.86.187.0/24
    allow from all
    </Limit>
    

上面这段代码来自威言威语的 [linux服务器(nginx或者apache)限制IP访问的方法](http://www.weisay.com/blog/linux-server-restrict-ip-method.html) 他最近饱受垃圾评论的攻击然后写了这篇文章。

## 通过 error 日志排查 PHP 错误

在虚拟主机中安装其他 PHP 网站的时候，老是出现问题提示 500 错误 ( 也就是 Apache 服务器错误 ) 。刚开始以为是没有给对应目录赋予 777 的权限导致 PHP 无法读取文件。于是我就赋予了权限，但是源码的 PHP 代码全都出来了而没有执行。

于是我就错误的以为 Apache 在虚拟主机下面，无法应用 PHP 环境，所以我就在网上搜索这方面的资料，当然一无所获。

然后我就重新测试，mv 到别的文件夹，然后新建 html 文件，访问正常，加上 `phpinfo();` 函数，正常显示。然后重新下载源码解压安装，服务器错误。简直郁闷死了，就这样折腾了大半天。

然后突然想起错误日志，于是看了一下 Apache 的 error 日志，几行眼熟的 PHP 的错误出来了。原来是权限问题和没有开启 PHP 的某项设置。然后就解决问题了。如果早看日志，也就不用做这么多测试了。

## 备份关键目录和文件

服务器也搭建好了，配置也配置了，运行也很正常。这时候应该备份一下关键的文件，如果日后的修改不正常了，再覆盖回去，这样就不需要重装系统来回折腾了。

备份通常使用 tar 命令将某个目录下的文件等，打包压缩成一个 tar.gz 文件存放在其他硬盘中或者下载下来。通常需要备份的目录有：

    /etc
    /home
    /boot
    /root
    和
    /usr/local
    /var/www
    

上面是一些配置文件和系统文件，下面的两个目录是程序安装目录和网页文件目录。你也可以按照你的服务器情况，备份更多的目录或者文件。使用如下命令对其打包 ( 以备份 /etc 目录为例 )：

    tar -zcvf /备份到某个目录/etc.backup.tar.gz /etc

这样就把整个 /etc 目录打包成了 etc.backup.tar.gz 文件，放在了某个目录下面。通常这个备份操作会写成 shell 然后交给 crontab 来按期自动执行，但是 shell 目前还不太会，以后会了再补充吧。

以上就是我配置 VPS 的时候遇到的一些坑，或许可以给你一些帮助。当然本人还是新手，如果上面有不对的地方，欢迎指正。