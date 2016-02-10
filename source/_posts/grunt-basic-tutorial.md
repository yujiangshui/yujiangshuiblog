title: Grunt 新手一日入门
tags:
  - grunt
id: 217
categories:
  - 前端相关
date: 2014-06-20 12:31:36
---

当时学习 Grunt 的时候，真是很头疼。分了两个时间段，学习了两次才硬啃下来，之后才能用在项目中。主要原因我认为是学习资料和文档上面写的太高端了。这类的文档或者资料有个显著特点，上来先简单介绍一下这个玩意（Grunt 是一个 JavaScript 任务运行器），然后就是如何安装，直接给你配置文件的语法，如何使用插件，新手往往看完还不知所以然。

<!-- more -->

就像我第一次学习的时候，只是大体知道 Grunt 很火，大家都在用，但耐着心看文档和一些别人的学习总结，还是困惑，这到底是个什么东西？究竟干什么用？为什么要这么麻烦配置这些东西？

到现在应用在项目中也有一小段时间了，稍微有一点点经验，好吧，我决定写一篇即便是新手也能马上看懂的文章。

## 用途和使用场景

先不要管 Grunt，我们先来看下微硬公司小明和小红的日常前端开发工作：

小明在开发一个 JS 插件，写了好多代码，终于写完了，放在 HTML 文件里调用一看，console 里面好多 error，于是就挨个调整修改。为了排除深层潜在问题，他还打开了 [http://www.jshint.com/](http://www.jshint.com/) 这个网站，把自己的代码复制进去用 jshint 检测了，结果发现了好多细节问题和不规范的地方，依次修改。最后要发布了，他又打开了 [http://tool.css-js.com/](http://tool.css-js.com/) 网站，把自己的代码复制进去，使用 Uglify 来压缩了一下，提供一个压缩版本。然后上传到了 Github 上托管。

这时候，Github 突然有一个 Issue，他看了一下，原来有个疏忽的地方，又进行了修改，然后打开 jsHint 和 Uglify 在线压缩网站进行检查和压缩，再次发布上去。

于此同时，小红在做一个活动页面的前端重构工作。她打开了正在做的 HTML 页面，由于使用了 Sass，所以她打开了 [Koala](http://koala-app.com/index-zh.html) 在后台帮她自动编译成 CSS，但是每次保存一下，想要看到效果，还是需要切换到浏览器，刷新一下。做交互处理的时候，她写了一些 JS，为了规范，也使用 jshint 检查了一下。终于做完了，这时候做了一下最后的优化处理准备上线，她把 icons 在 Photoshop 中合并成了一张图片，并在 CSS 的对应位置修改了一下，然后用在线压缩工具把 CSS 和 JS 都压缩了一遍，提交测试去了。

这就是他俩的工作，日复一日年复一年，有一天，小明终于受够了，朝小红发牢骚：哥要是再来回复制粘贴到网站上排错压缩，哥就是逗逼！小红满眼泪花的说，你想好办法了，帮我解决一下自动刷新问题，我也受够了。

于是小明工作都不做了，开始苦思冥想怎么开发一个可以自动把写的代码发送到远程网站，让他们检查一下错误并且压缩好，再反回来生成一个文件。

## 开发一个任务自动处理器

当然，小明很快就不那么想了，因为依赖外部网站有很多意外因素，而且受限于网络和网速，其次他们也没提供有关接口来调用。但是他们都提供了一些算法之类的工具等。这样可以在本地调用这些工具，来完成这些操作，甚至连网络也不需要。

小明愉快的开始在笔记本上构思这个工具的开发方法和需要的功能：

首先我需要先开发一个工具，可以调用这个工具对我的某个项目目录里面的项目文件做一些操作，比如压缩、查错、合并等。

如果要做成一个工具，可能不太好，或许别人还需要更多功能，但是我没法开发这么多功能啊。要不我就做个框架把，然后每个功能做成一个插件，比如压缩插件、合并插件。如果有人需要在他的项目里压缩某个文件，他安装一下我这个工具然后再安装压缩插件就好了。这样有更多需求的人，可以自己编写功能插件，然后配合我的工具使用。

慢着，他安装完了工具和插件之后，要怎么来调用这个插件来处理项目文件？在程序界面上选择文件，然后勾选选项？我的天，我就会写点 JS，哪里可能开发带有界面的程序？慢着，用 JS ？他可以在项目文件夹中编写一个 JS 来设置任务啊！然后我的工具会读取这个 JS，解析之后获得他要执行的任务（比如压缩某某文件并改名成某某），然后调用插件完成任务。

太棒了。但是插件这么多，放在项目里肯定很大，而且又是不相关代码，要不等他发布的时候自动删除这些插件文件把？不行，如果他要发给别人，别人要继续开发，还得重新安装依次安装这些插件，然后执行任务。那怎么办？要不我再用个文件记录一下当前项目中安装或者需要的插件把！这样只需要把这个文件和 JS 任务文件放在项目目录里面，有需要的人，直接输入一条命令安装一下，然后立刻就可以执行了。

我太聪明了！

![赞](http://jiangshui.b0.upaiyun.com/blog/2014/06/grunt0.jpg)

于是小明兴奋的跑过去给小红讲了一下他的工具的开发思路，然后告诉她，他可以帮忙写一个自动刷新的插件。小红反而淡定的说：等等，我好像见过这种东西，这不是 Grunt 嘛？

小明抓紧搜了一下 Grunt，看了一下文档之后，对着小红：尼玛，你为什么不早说！

## 开始学习 Grunt

Grunt 就是小明想的这样一种自动化任务处理工具，它就是一个工具框架，有很多插件扩展它的功能。

Grunt 基于 Node.js ，用 JS 开发，这样就可以借助 Node.js 实现跨系统跨平台的桌面端的操作，例如文件操作等等。此外，Grunt 以及它的插件们，都作为一个 包 ，可以用 NPM 安装进行管理。

所以 NPM 生成的 package.json 项目文件，里面可以记录当前项目中用到的 Grunt 插件，而 Grunt 会调用 Gruntfile.js 这个文件，解析里面的任务（task）并执行相应操作。

如果你对 Node.js、NPM 这些名词不太熟悉，建议先去搜索了解一下，因为下面的命令会涉及到它们，但是本文不会过多介绍。

### 安装 Grunt

Grunt 依赖 Node.js 所以在安装之前确保你安装了 Node.js。然后开始安装 Grunt。

实际上，安装的并不是 Grunt，而是 Grunt-cli，也就是命令行的 Grunt，这样你就可以使用 grunt 命令来执行某个项目中的 Gruntfile.js 中定义的 task 。但是要注意，Grunt-cli 只是一个命令行工具，用来执行，而不是 Grunt 这个工具本身。

安装 Grunt-cli 需要使用 NPM，使用下面一行即可在全局范围安装 Grunt-cli ，换句话说，就是你可以在任何地方执行 grunt 命令：

    npm install -g grunt-cli
    

需要注意，因为使用 －g 命令会安装到全局，可能会涉及到系统敏感目录，如果用 Windows 的话，可能需要你用管理员权限，如果用 OS X ／ Linux 的话，你可能需要加上 sudo 命令。

下面我们新建一个项目目录，并新建一些文件，这里我准备了一份很简单的项目，放在了 Github 上，下面操作将以此来操作，你可以下载或者 clone 下来： [https://github.com/yujiangshui/gruntxx](https://github.com/yujiangshui/gruntxx)

### 生成 package.json 文件

这个 package.json 文件其实是 Node.js 来描述一个项目的文件，JSON 格式。生成这个文件超级简单，推荐用命令行交互式的生成一下：

打开命令行，`cd gruntxx` 文件夹下面，输入指令 `npm init` 之后，就出来很多信息，然后开始填写项目名称，填写好了之后回车即可。其实这里你一路回车下去也无妨，但是建议你细细的填一下，不明白的跳过好了。

![npm init 填写项目信息](http://jiangshui.b0.upaiyun.com/blog/2014/06/grunt1.png)

填写好了之后，查看目录就会发现生成 package.json 文件了，这样就算生成好了。

其实就是一个文件而已，你觉得这种方式麻烦，完全可以新建一个文件，然后将类似下面的代码复制进去，改一下对应选项，保存成 package.json 文件就可以：

    {
      "name": "my-project-name",
      "version": "0.1.0",
      "devDependencies": {
      }
    }
    

最后我生成的代码如下：

    {
      "name": "gruntxx",
      "version": "0.0.1",
      "description": "学习 grunt",
      "repository": {
        "type": "git",
        "url": "https://github.com/yujiangshui/gruntxx.git"
      },
      "author": "Jiangshui",
      "license": "MIT",
      "bugs": {
        "url": "https://github.com/yujiangshui/gruntxx/issues"
      },
      "homepage": "https://github.com/yujiangshui/gruntxx"
    }
    

但这时我们还没有在项目文件中安装 Grunt 以及相关任务插件。

### 安装 Grunt 和所需要的插件

就现在的这个示例项目而言，我打算让 Grunt 帮忙实现下面几个功能：检查每个 JS 文件语法、合并两个 JS 文件、将合并后的 JS 文件压缩、将 SCSS 文件编译、新建一个本地服务器监听文件变动自动刷新 HTML 文件。

差不多就是这些，根据这些任务需求，需要用到：

*   合并文件：[grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat)
*   语法检查：[grunt-contrib-jshint](https://github.com/gruntjs/grunt-contrib-jshint)
*   Scss 编译：[grunt-contrib-sass](https://github.com/gruntjs/grunt-contrib-sass)
*   压缩文件：[grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify)
*   监听文件变动：[grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch)
*   建立本地服务器：[grunt-contrib-connect](https://github.com/gruntjs/grunt-contrib-connect)

它们的命名和文档都很规范，因为这些是官方提供的比较常用的插件。这些插件同时都是 NPM 管理的包，比如 [grunt-contrib-concat - npm](https://www.npmjs.org/package/grunt-contrib-concat) 你也可以在这上面看到用法等。

下面我们就要在这个项目中安装这些插件，执行命令：

    npm install grunt --save-dev
    

表示通过 npm 安装了 grunt 到当前项目，同时加上了 --save-dev 参数，表示会**把刚安装的东西添加到 package.json 文件中**。不信你打开 package.json 文件看下，是不是多了

      "devDependencies": {
        "grunt": "^0.4.5"
      }
    

没错，这个的意思就是当前项目依赖 grunt，后面是它的版本，咱们不用管。如果安装的时候没有添加 --save-dev 参数，这里就不会出现了，你需要自行添加上去。

下面我们来安装 Grunt 的插件，当然，不需要一个个的安装，太麻烦了，我们可以：

    npm install --save-dev grunt-contrib-concat grunt-contrib-jshint grunt-contrib-sass grunt-contrib-uglify grunt-contrib-watch grunt-contrib-connect
    

等待一大串乱七八糟的下载状态，我们把 grunt 和相关插件都安装好了，不信看下是不是多了一个 node_modules 文件夹？打开看下，里面就是咱们刚安装的插件。

### 配置 Gruntfile.js 的语法

插件也装好了，开始写任务吧！既然是要程序来读取执行，必要要有一定的语法规范，下面来简单的说一下：

首先要明白，这是一个 JS 文件，你可以写任意的 JS 代码，比如声明一个 对象 来存储一会要写任务的参数，或者是一个变量当作开关等等。

然后，所有的代码要包裹在

    module.exports = function(grunt) {
        ...
    };
    

里面。没有为什么。

在这里面的代码，除去你自己写的乱七八糟的 JS，与 Grunt 有关的主要有三块代码：**任务配置代码**、**插件加载代码**、**任务注册代码**。

顾名思义，这三块代码，任务配置代码就是调用插件配置一下要执行的任务和实现的功能，插件加载代码就是把需要用到的插件加载进来，任务注册代码就是注册一个 task，里面包含刚在前面编写的任务配置代码。

这样，就可以用 grunt 来执行注册的一个 task 从而根据任务配置代码调用需要的插件来执行相应的操作。

下面来分别看一下这三块代码的写法。

#### 任务配置代码

例如下面代码：

      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        uglify: {
          options: {
            banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
          },
          build: {
            src: 'src/<%= pkg.name %>.js',
            dest: 'build/<%= pkg.name %>.min.js'
          }
        }
      });
    

可以看出，具体的任务配置代码以对象格式放在 `grunt.initConfig` 函数里面，其中先写了一句 `pkg: grunt.file.readJSON('package.json')` 功能是读取 package.json 文件，并把里面的信息获取出来，方便在后面任务中应用（例如下面就用了 `<%= pkg.name %>` 来输出项目名称），这样可以提高灵活性。之后就是 uglify 对象，这个名字是固定的，表示下面任务是调用 uglify 插件的，首先先配置了一些全局的 options 然后新建了一个 build 任务。

也就是说，在 Uglify 插件下面，有一个 build 任务，内容是把 XX.js 压缩输出到 xx.min.js 里面。如果你需要更多压缩任务，也可以参照 build 多写几个任务。

至于怎么写出来 options 里面的参数和 build 里面的参数内容，这才是 grunt 学习的难点，你需要查看每个插件的用法，根据用法来编写任务，可以看下 [grunt-contrib-uglify 的官方文档](https://github.com/gruntjs/grunt-contrib-uglify)，往下面拉你就可以看到参数和使用方法了。

这样，我们就新建了一个基于 uglify 的任务 build，功能是把 `src/<%= pkg.name %>.js` 压缩输出 `build/<%= pkg.name %>.min.js`。

#### 插件加载代码

这个就超级简单了，由于上面任务需要用到 grunt-contrib-uglify，当 grunt-contrib-uglify 安装到我们的项目之后，写下下面代码即可加载：

    grunt.loadNpmTasks('grunt-contrib-uglify');
    

#### 任务注册代码

插件也加载了，任务也布置了，下面我们得注册一下任务，使用

    grunt.registerTask('default', ['uglify']);
    

来注册一个任务。上面代码意思是，你在 default 上面注册了一个 Uglify 任务，default 就是别名，它是默认的 task，当你在项目目录执行 grunt 的时候，它会执行注册到 default 上面的任务。

也就是说，当我们执行 grunt 命令的时候，uglify 的所有代码将会执行。我们也可以注册别的 task，例如：

    grunt.registerTask('compress', ['uglify:build']);
    

如果想要执行这个 task，我们就不能只输入 grunt 命令了，我们需要输入 `grunt compress` 命令来执行这条 task，而这条 task 的任务是 uglify 下面的 build 任务，也就是说，我们只会执行 uglify 里面 build 定义的任务，而不会执行 uglify 里面定义的其他任务。

**这里需要注意的是，task 的命名不能与后面的任务配置同名，也就是说这里的 compress 不能命名成 uglify，这样会报错或者产生意外情况**

OK，加上这三块代码，我们的示例 Gruntfile.js 就是这样子的：

    module.exports = function(grunt) {

      // Project configuration.
      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        uglify: {
          options: {
            banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
          },
          build: {
            src: 'src/<%= pkg.name %>.js',
            dest: 'build/<%= pkg.name %>.min.js'
          }
        }
      });

      // Load the plugin that provides the "uglify" task.
      grunt.loadNpmTasks('grunt-contrib-uglify');

      // Default task(s).
      grunt.registerTask('default', ['uglify']);

    };
    

这就是官方那个坑爹示例，貌似 uglify 的参数好像不对，反正我之前学习的时候，没法运行这个配置，下面我们来根据示例项目和我们的需求配置一下。

### 配置 Gruntfile.js

先从简单的入手，我们先来配置一下编译 Scss 文件的 task。先新建一个 Gruntfile.js 文件，把大体的配置结构复制进去：

    module.exports = function(grunt) {

      var sassStyle = 'expanded';

      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        sass: {

        }
      });

      grunt.loadNpmTasks('grunt-contrib-sass');

      grunt.registerTask('outputcss',['sass']);
      grunt.registerTask('default');

    };
    

应该可以看懂把？这里不再赘述了，我们来根据 [Sass 文档](https://github.com/gruntjs/grunt-contrib-sass)，编写一个 Sass 任务 output ：

    module.exports = function(grunt) {

      var sassStyle = 'expanded';

      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        sass: {
          output : {
            options: {
              style: sassStyle
            },
            files: {
              './style.css': './scss/style.scss'
            }
          }
        }
      });

      grunt.loadNpmTasks('grunt-contrib-sass');

      grunt.registerTask('outputcss',['sass']);
      grunt.registerTask('default');

    };
    

意思就是将 `./scss/style.scss` 这个文件以 sassStyle 变量存储的方式编译成 根目录下面的 style.css 文件。

下面拿起命令行，cd 到当前文档目录，执行一下 grunt 命令，结果报错 undefined，没错，因为我们的 default task 里面没有定义任何任务，然后执行 `grunt outputcss` 命令，提示编译 Scss 文件成功，当然前提是你的 Scss 语法正确，如果有问题就不会成功。

![grunt 执行任务](http://jiangshui.b0.upaiyun.com/blog/2014/06/grunt2.png)

下面我们打算先把 src 目录下面的两个 JS 文件合并起来，然后再用 jshint 检测一下是否有语法问题，如果正确，再用 uglify 对合并起来的文件进行压缩。

参照 [grunt-contrib-concat 的官方文档](https://github.com/gruntjs/grunt-contrib-concat)，我们可以写出下面的任务：

    module.exports = function(grunt) {

      var sassStyle = 'expanded';

      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        sass: {
          output : {
            options: {
              style: sassStyle
            },
            files: {
              './style.css': './scss/style.scss'
            }
          }
        },
        concat: {
          options: {
            separator: ';',
          },
          dist: {
            src: ['./src/plugin.js', './src/plugin2.js'],
            dest: './global.js',
          },
        }
      });

      grunt.loadNpmTasks('grunt-contrib-sass');
      grunt.loadNpmTasks('grunt-contrib-concat');

      grunt.registerTask('outputcss',['sass']);
      grunt.registerTask('concatjs',['concat']);
      grunt.registerTask('default');

    };
    

执行 `grunt concatjs` 之后，就会发现根目录多了一个 global.js 文件，里面是两个文件合并起来的。然后类似的继续看 uglify 和 jshint 的文档，我们就可以根据需求写出下面任务：

    module.exports = function(grunt) {

      var sassStyle = 'expanded';

      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        sass: {
          output : {
            options: {
              style: sassStyle
            },
            files: {
              './style.css': './scss/style.scss'
            }
          }
        },
        concat: {
          options: {
            separator: ';',
          },
          dist: {
            src: ['./src/plugin.js', './src/plugin2.js'],
            dest: './global.js',
          },
        },
        uglify: {
          compressjs: {
            files: {
              './global.min.js': ['./global.js']
            }
          }
        },
        jshint: {
          all: ['./global.js']
        }
      });

      grunt.loadNpmTasks('grunt-contrib-sass');
      grunt.loadNpmTasks('grunt-contrib-concat');
      grunt.loadNpmTasks('grunt-contrib-jshint');
      grunt.loadNpmTasks('grunt-contrib-uglify');

      grunt.registerTask('outputcss',['sass']);
      grunt.registerTask('concatjs',['concat']);
      grunt.registerTask('compressjs',['concat','jshint','uglify']);
      grunt.registerTask('default');

    };
    

其中注册了一个 compressjs 任务 `grunt.registerTask('compressjs',['concat','jshint','uglify']);` 意思就是依次执行 合并、检查、压缩 任务。我们把刚生成的 global.js 文件删掉，在命令行执行 `grunt compressjs` 任务，结果 jshint 报错了：

![grunt jshint 报错](http://jiangshui.b0.upaiyun.com/blog/2014/06/grunt3.png)

grunt 遇到错误就退出了，就没法继续执行下面的任务。通过错误提示可以发现，是因为 concat 里面设置的参数——在两个文件合并间插入一个“;”——这本来是为了防止两个文件之间相互干扰设置的，结果无法被 jshint 验证通过，我们可以删掉这个参数，或者设置 jshint 验证这两个文件，然后再进行合并。

为了方便，我删掉了这个参数。再执行一下，成功了，项目目录里面多了 global.js 和 global.min.js 文件。

小明看到这里，痛哭流泪，自己每次打开好几个网站，辛苦挨个粘贴复制新建，没想到输入一条命令就可以了。不过让他更伤心的还在后面，连这些命令都不用重复输入。

我们可以通过 watch 来监听文件变动，当文件变化了（我们编写保存了），自动执行某些任务。此处为了节约版面，我连自动刷新的任务一块写上去。根据 [grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch) 和 [grunt-contrib-connect](https://github.com/gruntjs/grunt-contrib-connect) 这俩文档，我们可以写出下面的任务：

    module.exports = function(grunt) {

      var sassStyle = 'expanded';

      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        sass: {
          output : {
            options: {
              style: sassStyle
            },
            files: {
              './style.css': './scss/style.scss'
            }
          }
        },
        concat: {
          dist: {
            src: ['./src/plugin.js', './src/plugin2.js'],
            dest: './global.js',
          },
        },
        uglify: {
          compressjs: {
            files: {
              './global.min.js': ['./global.js']
            }
          }
        },
        jshint: {
          all: ['./global.js']
        },
        watch: {
          scripts: {
            files: ['./src/plugin.js','./src/plugin2.js'],
            tasks: ['concat','jshint','uglify']
          },
          sass: {
            files: ['./scss/style.scss'],
            tasks: ['sass']
          },
          livereload: {
              options: {
                  livereload: '<%= connect.options.livereload %>'
              },
              files: [
                  'index.html',
                  'style.css',
                  'js/global.min.js'
              ]
          }
        },
        connect: {
          options: {
              port: 9000,
              open: true,
              livereload: 35729,
              // Change this to '0.0.0.0' to access the server from outside
              hostname: 'localhost'
          },
          server: {
            options: {
              port: 9001,
              base: './'
            }
          }
        }
      });

      grunt.loadNpmTasks('grunt-contrib-sass');
      grunt.loadNpmTasks('grunt-contrib-concat');
      grunt.loadNpmTasks('grunt-contrib-jshint');
      grunt.loadNpmTasks('grunt-contrib-uglify');
      grunt.loadNpmTasks('grunt-contrib-watch');
      grunt.loadNpmTasks('grunt-contrib-connect');

      grunt.registerTask('outputcss',['sass']);
      grunt.registerTask('concatjs',['concat']);
      grunt.registerTask('compressjs',['concat','jshint','uglify']);
      grunt.registerTask('watchit',['sass','concat','jshint','uglify','connect','watch']);
      grunt.registerTask('default');

    };

添加了 connect 任务，用来新建一个本地服务器，以当前目录作为服务器根目录，然后添加 watch 任务，监听 Scss 文件变动，如果变了，执行一下 sass 任务，监听那俩 JS，如果变了，执行 合并、检查、压缩 任务，监听 html、css、js 文件，如果变动，livereload 自动刷新打开的页面。

而注册的 watchit task 就是我们的终极 task，第一次执行，先编译 sass、再合并、检查、压缩、开启服务器、监听文件变动。我们执行一下 `grunt watchit` 就可以看到效果了，你可以修改一下 scss 文件，把字体设置大一点，切换到浏览器的时候，就看到了实时刷新效果。也可以修改一下 JS，故意改错一下，会发现 jshint 会提示你出错了。

![grunt watch task](http://jiangshui.b0.upaiyun.com/blog/2014/06/grunt4.png)

看到这里，小明和小红相拥而泣。。。。

### 项目文件传输与协作

项目开发完成之后，往往需要 push 到 Github 或者上传 FTP 等。或许其他人会接手你的项目继续开发，或者你会换台电脑进行开发。

当小明用 git 上传 Github 的时候，傻了眼，项目里 node_modules 文件夹下面的东西要十几M呢，这比我项目本身还大，上传下载都不方便。

其实这些插件和 grunt 不需要上传，因为有 package.json 这个文件记录了你这个项目中依赖的 grunt 插件，你只需要上传这个文件即可。下载下来之后，只需要在这个项目文件夹下面，输入命令 `npm install`，NPM 会自动读取 package.json 文件，将 grunt 和有关插件给你下载下来，很方便的。

也不需要在本地上传的时候删除，用 git 的话，可以使用 [.gitignore 文件](https://help.github.com/articles/ignoring-files)来过滤掉这个文件夹，禁止 git 追踪。

## 总结与扩展阅读

Grunt 就是这样一种任务自动运行器，应用好它可以减轻很多不必要的人工操作，只需要专注 coding 就可以。甚至还有[Grunt 插件帮你自动完成 CSS Sprite](https://github.com/Ensighten/grunt-spritesmith)，更多功能还需要你自己去摸索。

新手看完本文，再看一下 [Grunt 官方文档](http://gruntjs.com/getting-started) 应该没有太多疑问了，那就再看一遍把。当然也有[中文版](http://www.gruntjs.org/)。

除了 Grunt 之外，同类型比较火的还有 Gulp 这个工具。其实两个东西的功能是一样的，只不过是任务配置 JS 的语法不同，Gulp 的 Gulpfile.js 的写法更加通俗易懂，上手更快。但是 Gulp 的插件等感觉不如 Grunt，Grunt 官方提供了一些常见的插件，满足大部分日常工作，而且可靠值得信赖，而 Gulp 好像没有太多官方出品，各种插件不太规范。简单的说，Grunt 和 Gulp 就像 iPhone 与 Android 一样，一个质量高学习难一点，一个学起来简单但是有点那个，你懂得。

此外，可以看一些高手的项目，你会发现更好的 Grunt 用法，比如 Yeoman 生成的项目，就有很完善的 Grunt 任务和插件，此外，[jQuery](https://github.com/jquery/jquery) 等也用 Grunt 进行打包，这些 Grunt 文件你都可以查看研究一下他们的写法和用法，受益匪浅。

最后，如果你懒得跟着文章一点点的配置示例项目，你也可以跳转到[示例项目的 grunt 分支](https://github.com/yujiangshui/gruntxx/tree/grunt)，这里面是我配置好的，你需要先 `npm install` 然后就可以直接执行那些命令。