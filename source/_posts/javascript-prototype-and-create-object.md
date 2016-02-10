title: JavaScript 原型理解与创建对象应用
id: 220
categories:
  - JavaScript
date: 2014-06-30 04:57:47
tags:
---

这段时间把之前的 JavaScript 的笔记复习了一遍，又学习了一些新的内容，所以把自己的学习笔记加上个人理解在这里总结一下，并提供一个简单的应用示例，希望能帮助一些刚入门的朋友。主要参考《JavaScript 高级程序设计》一书，你可以在上面看到更详细的内容。

<!-- more -->

## 基础知识

之前看不懂原型之类的时候，其实是对基础知识掌握的不好，后来认真阅读了数据类型等等基础知识之后，再看一些难度更大的内容，就有了自己的新理解。所以在这里简单的回顾一下有关的内容。

### 引用类型

引用类型主要包括：Object 类型、Array 类型、Date 类型、RegExp 类型、Function 类型等等。

引用类型使用时，需要从它们身上生成一个对象（实例）。也就是说，引用类型相当于一个模版，当我们想要用某个引用类型的时候，就需要用这个模版来生成一个对象来使用，所以引用类型有时候也称作对象定义。

例如，我们需要生成一个 person 对象，来定义某人的个人信息和行为，那么我们就需要依赖 Object 类型：

    var person = new Object();
    person.name = "jiangshui";
    person.sayName = function(){
        console.log(this.name);
    }
    

上面的这个 person 对象，通过 new 操作符使用 Object 类型这个“模版”定义。之后就可以对这个对象添加属性 name 和方法 sayName 了。属性和方法是 Object 类型具有的“功能”，所以通过 Object 等引用类型创建的对象就可以用这个了。

创建对象不一定非得需要用 new 操作符，有一些类型可以简化的创建，例如创建一个上面那样的 Object 类型的对象，也可以使用下面两种方法：

    var person = {};
    person.name = "jiangshui";
    person.sayName = function(){
        console.log(this.name);
    }
    

或

    var person = {
        name : "jiangshui",
        sayName : function(){
            console.log(this.name);
        }
    };
    

`{}`操作符的功能就跟 `new Object()` 一样，简化了操作。上面两种写法也有一些区别，**第一种是“追加”，也就是在之前的定义中，继续添加属性或者方法，如果之前已经存在了同名属性方法，则会覆盖。而第二种是“取代”，就是不管前面是否定义 person 对象的属性和方法，这个方法会用新定义的内容，整个替换掉之前定义的。因为引用类型生成的对象，是储存在内存中的一块区域，然后将其指针保存在某变量中（person），第二种写法，是生成了一个新对象（新内存区域），然后将 person 变量指向了新内存区域，所以就把之前的取代了。**了解这一点对后面理解，至关重要。

其他引用类型的用法大致一致，例如 Array 类型，也可以用 `[]` 来生成对象，或者直接定义。生成数组对象之后，就可以按照数组的格式存储信息内容，此外对象会得到 Array 类型中定义的那些方法，例如 push、shift、sort 等等，就可以调用这些方法，例如：

    var colors = [];
    colors.push('red','green');
    console.log(colors);
    

上面代码就是通过 Array 类型创建一个数组类型的对象，然后调用 Array 类型里面之前定义的 push 方法，向对象里面添加了 red 和 green 两个值，最后在控制台打印出来，就可以看到了。

后面会介绍怎么向 Array 等类型中添加像 push 这样的方法，所以理解上面这个，就非常重要了。

### call 和 apply 方法

这两个方法是 Function 类型提供的，也就是说，可以在函数上面使用。call 和 apply 方法的功能一样，就是可以扩充函数运行的作用域，区别就在于使用 call 的时候，传递给函数的参数必须逐个列举出来，而 apply 方法却不用。这样可以根据自己函数的要求来决定使用 call 或者 apply。

扩充函数运行的作用域是什么意思？举个例子你就明白了。

你可以这样理解，函数被包裹在一个容器（作用域）里面，在这个容器里面存在一些变量或者其他东西，当函数运行，调用这些变量等，就会在当前容器里面找这个东西。这个容器其实外面还包裹了一个更大的容器，如果当前小容器没有的话，函数会到更大的容器里面寻找，依次类推，一直找到最大的容器 window 对象。但是如果函数在当前小容器里面运行的时候，小容器里面有对应变量等，即便是大容器里面也有，函数还是会调用自己容器里面的。

而 call 和 apply 方法，就是解决这个问题，突破容器的限制。就前面例子：

    var person = {
        name : "jiangshui",
        sayName : function(){
            console.log(this.name);
        }
    };
    

打开 Chrome 的 Console 之后，粘贴进去执行一下，之后再执行 `person.sayName()` 可以看到

![函数作用域](http://jiangshui.b0.upaiyun.com/blog/2014/06/prototype0.png)

这时候，person 就是一个容器，其中创建了一个 sayName 方法（函数），执行的时候，必须在 person 作用域下面执行。当在最下面直接执行的时候，也就是在 window 的作用域下面执行会报错 not defined，因为 window 下面没有定义 sayName 方法。而里面的 this 指针，是一个比较特殊的东西，它指向当前作用域，`this.name` 的意思，就是调用当前作用域下面的 name 值。

下面我们为 window 对象添加一个 name 属性：

    window.name = "yujiangshui";
    

或者直接

    name = "yujiangshui";
    

因为 window 是最大的容器，所以 window 可以省略掉，所有定义的属性或者变量，都挂靠到 window 上面去了，不信可以看：

![window 是最大的容器](http://jiangshui.b0.upaiyun.com/blog/2014/06/prototype1.png)

那现在我们就想在 window 这个大容器下面，运行 person 小容器里面的 sayName 方法，就需要用 call 或 apply 来扩充 sayName 方法的作用域。执行下面语句：

    person.sayName.call(window);
    

或者

    person.sayName.call(this);
    

输出的结果都是一样的，你也可以换用 apply 看看效果，因为这个 demo 太简单的，不需要传递参数，所以 call 和 apply 功能效果就完全一致了。

![call 和 apply 效果](http://jiangshui.b0.upaiyun.com/blog/2014/06/prototype2.png)

解释一下上面代码，sayName 首先是 Function 类型的实例，也就具有了 call 方法和 apply 方法，call 和 apply 方法既然是 Function 类型的方法，所以就需要用这种方式调用 `person.sayName.call(window)` 而不是什么 `person.sayName().call(window)` 之类的。

然后 call 和 apply 方法的参数，就是一个作用域（对象），表示将前面的函数在传递进去的作用域下面运行。将 window 这对象传递进去之后，sayName 方法中的 this.name 指向的就是 window.name，于是就扩充了作用域。

为什么传递 window 和 this 都是一样的效果？因为我们当前执行这个函数的位置是 window，前面说过 this 指针指向的是当前作用域，所以 this 指向的就是 window，所以就等于 window。

### 原型是什么

Function 类型有一个属性 prototype，直接翻译过来就是原型。这个属性就是一个指针，指向一个对象，这个对象包含一些属性和方法，这些属性和方法会被当前函数生成的所有实例（对象）所共享。

这句话根据前面所说的，细细琢磨下来，就可以得到下面代码：

    function Person(){
        ...
    }
    Person.prototype = {
        country : 'china',
        sayName : function(){
            ...
        }
    }
    

先创建了一个 Function 类型的实例 person，然后 person 的方法 prototype 是一个对象，就声明指向了一个对象。这个对象里面的属性和方法，会被当前 person 函数生成的实例所共享。也就是说：

    person1 = new Person();
    person2 = new Person();
    

person1 和 person2 都是通过 Person 这个 Function 类型实例，再次生成的实例，它们俩都有共同的属性 country 和方法 sayName，因为它们都有某个指针（__proto__），直接指向 Person.prototype 所指向的对象。不过要注意 __proto__ 这个指针是不标准的，只有 Chrome 和 Firefox 等浏览器自己定义的，实际中，也不会用到这个属性，只是作为理解 prototype 来用：

![__proto__  指针效果](http://jiangshui.b0.upaiyun.com/blog/2014/06/prototype4.png)

关于原型等用法，后面会更具体的讲到。

## 创建对象的模式

下面，我们就来看下创建对象的方法和常用模式，以及它们之间的优缺点。

### 工厂模式

就像工厂一样，抽象了创建具体对象的过程，用函数来封装以特定接口创建对象的细节。通过使用函数代替部分重复工作，代码如下：

    function createPerson(name, age, job){
        var o = new Object();
        o.name = name;
        o.age = age;
        o.job = job;
        o.sayName = function(){
            alert(this.name);
        };
        return o;
    }
    var person1 = createPerson("jiangshui","22","engineer");
    

这样就创建出来了一个人，工厂模式解决了多个相似对象重复创建问题，但是没有解决对象识别问题。只是单纯的创建了一个对象，而不管这个对象是从人类模版还是动物模版创建的，无法区分这个对象的类型。

### 构造函数模式

创建一个自定义的构造函数，从而定义自定义对象类型的属性和方法。

    function Person(name, age, job){
        this.name = name;
        this.age = age;
        this.job = jpb;
        this.sayName = function(){
            alert(this.name);
        };
    };
    var person1 = new Person(...);
    

构造函数模式与工厂模式区别：

*   没有显式的创建对象。
*   直接将属性和方法赋值 this 对象。
*   没有 return 语句。

Person 是 Function 类型的对象，new 之后，会继续产生一个**对象**，但这个新产生的对象，由于在函数中传递进去参数，并赋值给了 this 指针，那么传递进去的内容，就变成了新产生对象的属性或方法。

构造函数默认习惯是首字母大写，上面代码执行经历了下面几个步骤：

1.  创建一个新对象
2.  将构造函数作用域赋值给新对象
3.  执行构造函数中的代码
4.  返回新对象

这样生成的实例中，都默认包含一个 `constructor` 属性指向构造函数，例如：

    alert(person1.constructor == Person);
    

所以用构造函数模式，有类型的区分，可以将它的实例标识为一种特定的类型。

此外，构造函数就是普通的函数，因为要反馈得到新对象，所以用 new 来调用。如果不用的话，直接执行就跟普通函数一样，例如上面，执行 `Person.sayName()` 会弹出 `window.name`，因为函数在 window 下面执行，所以 this 指向 window。

构造函数模式也是有缺陷的，构造函数模式里面的方法，在每个实例上都重新创建了一遍，因此不同实例上的同名函数是不相等的。例如：

    person1.sayName == person2.sayName; //false
    

也就是说，**由构造函数生成的每个对象实例，属性和方法都是独有的，都是复制了一遍**。属性独有是必须的，因为这正是对象之间不同的地方，但是很多方法功能和代码都是一样的，重复复制多次，显然就会浪费资源。

所以我们可以把函数放在外面，然后在构造函数里面，用指针指向这个函数，那么生成的实例中，方法存储的就是一个指向某函数的指针，也就共用一个函数了：

    function Person(name, age){
        this.name = name;
        this.age = age;
        this.sayName = sayName;
    }
    function sayName(){
        alert(this.name);
    }
    

但是这样，这个函数就变成了全局函数，而且与 Person 构造函数关联性不强，没有封装性可言。

下面有请原型模式登场。

### 原型模式

前面已经介绍了一部分关于原型的基础知识。简单的说，就是每个函数都有一个 prototype 属性，指向一个对象（原型对象），这个对象里面可以放一些属性或者方法。然后这个函数生成的实例，会有一个不规范的属性（__proto__）指向原型。

由此来看，你应该可以理解：**prototype 产生的属性和方法是所有实例共享的**。

这样正好解决了上面构造函数模式中，实例中函数的共用问题。例如下面代码：

    function Person(){
        ....
    }
    Person.prototype.name = "jiangshui";
    Person.prototype.sayName = function(){
        alert(this.name);
    };

    var person1 = new Person();
    person1.sayName(); //jiangshui
    

或者

    Person.prototype = {
        constructor : Person,
        name : "jiangshui",
        sayName : function(){
            alert(this.name);
        }
    };
    

第二种方法覆盖了整个 prototype 对象，所以需要手动指定 constructor 属性，指向构造函数否则会指向 Object。

梳理一下它们的关系：

*   Person.prototype －》 原型对象，可以定义一些属性或者参数，被所有实例共用。
*   Person.prototype.constructor == Person —》 原型对象有个默认的属性 constructor 指向该原型对象所属的构造函数（注意另一种写法会覆盖掉这个属性，需要重新指定）。
*   person1 = new Person() －》构造函数生成实例，实例包含了构造函数的内容和原型对象的内容。
*   person1.__proto__ －》指向创建这个实例的原型对象（不规范，不要用）。

使用 isPrototypeOf() 可以确定对象之间的关系。例如：

    Person.prototype.isPrototypeOf(person1);
    

**当代码读取某个对象的某个属性，会执行搜索**。先从当前对象开始，如果没有，则搜索指针指向的原型对象，而不会搜索构造函数。对象实例可以访问但是不能重写原型对象的值。如果实例中设置了与原型对象同名的属性，则搜索过程，在实例中结束而不会访问原型对象，所以达到覆盖的目的。因此即使这个属性设置为 null，也表示在实例中已经存在该属性，而不会取消掉这个属性，从而可以访问原型对应属性。

所以需要使用 delete 操作符，完全删除实例属性，从而可以重新访问原型。

原型是动态的，**对原型对象所做的任何修改，都能立即从实例上反映出来**。原因是实例与原型之间的松散链接关系，每次调用实例的属性方法，都会进行一次查询，如果原型变了，查询结果也就变了。

了解原型之后，我们也可以对原生对象添加新方法或属性。Object、Array、String 等原生引用类型，与上面构造函数类似，我们可以用 prototype 扩充它们的方法。例如：

    String.prototype.startsWith = function(text){
        return this.indexOf(text) == 0;
    };
    var msg = "Hello World";
    msg.startsWith("Hello");
    

这段代码为 String 这个原生引用类型，增加了一个 startsWith 方法，功能就是传递进去一个参数，看看要测试的字符串是否以参数开始。由于原型的动态性，所以只要执行一下，所有字符串类型的变量全都获得了这个方法。

但是不推荐使用这个方法，如果用的太多，代码太多，会导致维护困难、代码混乱等情况。一般情况下，会先继承某个原生引用类型，然后再在新自定义的类型上创建。关于继承，后面会再总结。

原型模式也不是万能的，原型中的所有属性和方法是被所有实例共享的，所以对于函数之类非常合适，而对于包含引用类型的属性来说，就会产生一些冲突。例如：

    function Person(){}
    Person.prototype = {
        constructor : Person,
        friends : ["greg","jack"]
    };
    var person1 = new Person();
    var person2 = new Person();
    person1.friends.push("tom");
    console.log(person2.friends);
    

你会在 console 中看到，person2 的 friends 多了一个 tom，这并不是我想要的，但是对 person1 定义他的朋友时，的确影响到了实例 person2。

所以我们要结合原型模式和构造函数模式来使用。

### 组合使用构造函数模式和原型模式

这就是最常用的模式，构造函数用来定义实例属性，通过传递参数实现自定义；原型用来定义方法或者需要所有实例共享的属性。这样，既实现了自定义，又保证了共用，还避免了问题。

    function Person(name, age, job){
        this.name = name;
        this.age = age;
        this.job = job;
        this.friends = ["greg","jack"];
    }
    Person.prototype = {
        constructor : Person,
        sayName : function(){
            alert(this.name);
        }
    };
    var jiangshui = new Person("jiangshui","22","engineer");

## 实际应用示例

OK，到了这里，你可能会看懂原型是啥，以及如何创建对象，可是，这些又有什么用？确实，我之前的工作，一直也就是用 jQuery 写一些代码就可以了，根本用不到封装然后生成对象实现功能等。那这些究竟有什么用？

这种开发方式主要用于模块化和组建化的开发。比如你常用的弹窗功能，你当然可以把弹窗有关代码，每次都粘贴复制，然后修改一下就可以用在项目里面了。更好的选择是把你的弹窗功能代码，抽象封装成这样的一个组件，这样当你需要用弹窗的时候，只需要传递参数生成一个弹窗实例，就可以调用了。

特别是大型的项目，和多人协作的项目，配合上现在的 require.js 和 sea.js 进行模块化的开发，这种方式还是很有必要的。这里推荐一下阿当制作的[《阿当大话西游之WEB组件》](http://www.imooc.com/learn/99)，看完你就会有个大体的了解了。