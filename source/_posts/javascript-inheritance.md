title: JavaScript 继承简单理解与实现
id: 223
categories:
  - JavaScript
date: 2014-07-02 03:52:45
tags:
---

## 基础知识

由于之前打算将继承和原型一起写，但后来篇幅太大只能分开，所以部分内容放在了之前那篇原型文章（[JavaScript 原型理解与创建对象应用](http://yujiangshui.com/javascript-prototype-and-create-object/)），强烈建议先看一下那篇文章。

本文参考自己学习《JavaScript 高级程序设计》的笔记，加上自己的个人理解，有不对之处还望指出。此外在《JavaScript 高级程序设计》书中对应部分可以看到更详细的内容和图表。

<!-- more -->

### 继承是什么

继承是面向对象编程的一种特性，其实开发一个东西跟造物主创造世界一样，那我就通过造物主创造世界来解释一下面向对象和继承。

造物主造了一些动物，造人的时候，因为每个人都不一样，所以他要一个一个的制造，也就是先做一个人类的模型，然后给这个人的模型，添加各种参数（身高、姓名、体重等），然后再添加各种行为（走路能力、手臂摆动、移动货物、跑多块、跳多高）。

由于要造的人太多，造物主造烦了，于是他观察之前造出来的人，好多工作都是重复的，比如有一大批人都有走路这个行为，也可以手臂摆动，好多人身高等也相同，于是他就用了一个工具（function）来创建人类，首先设置好工具的功能（创建一个人模型，然后添加各种参数和行为），然后向工具中传递一些个性的东西（例如姓名等）。这样造人的时候，就方便多了，只需要向这个工具中传递一些人与人不同的东西（例如姓名），就可以造出一个人来，省下了大量的重复工作。

造出的人就是一个对象，这个对象包含了一些属性和方法，分别就是姓名、身高和走动、吃饭等等。

造物主继续造其他动物，突然发现对动物的某个属性设置错了，于是不得已把所有动物又修改了一边，这时候发现，动物和人类也有共同之处，有一部分重复的工作，于是造物主就想出了继承这个解决方法。大体思路是这样的：

动物和人都有一些共同的属性和行为，包括名字、重量和吃东西、排泄行为等等。这里要进行更高层的抽象，所以显然不能用姓名和体重之类，因为动物没有姓名、体重这类名字，而需要用名字、重量。

造物主抽象出来了这一切，命名为了“动物”这个模版。然后造物主要创建“陆上动物”这个模版，陆上动物也是动物，具有动物所有的属性和行为，并且增加了奔跑等行为，造物主就让“陆上动物”模版继承了“动物”模版，然后对“陆上动物”模版增加了奔跑行为。同样的，“海上动物”这个模版，就没有奔跑行为了，但是具有了游泳行为。

人类属于陆上动物，所以造物主定义“人类”模版继承了“陆上动物”的所有内容，并添加了人类独有的站立行走、感情等等属性和行为。至于“蛇类”，造物主定义它们没有腿、有毒等等属性和行为。

然后开始创造人这个个体，这时候已经有了人类这个模版，只需要基于这个模版，生成一个实例，然后对这个实例添加每个人不同的属性和行为，这样就创建了一个个个体。

造物主突然发现，自己设置的陆上动物奔跑速度太低了，他没有挨个把所有陆上动物重新设置一遍，而是找到“陆上动物”这个模版，设置了一下“奔跑速度”这个属性，所有继承“陆上动物”的实例，奔跑速度全都被更新好了。

造物主对现在的工作太满意了，创建新个体和修改之前的 BUG 都特别方便。

所以说，上帝是个程序员。

这就是面向对象编程的大体思路以及继承的作用，先定义高度抽象的基类，然后再继承基类扩展出来更详细的类，然后不断的具体具体，直到可以创建具体个体的类。这时候，就可以直接通过这个类创建个体来实现相关功能。

## 继承的实现方法

JavaScript 中的继承比较奇葩，无法实现接口继承，只能依靠原型继承。

### 原型链

前面文章已经介绍过原型了，原型就是一个对象，通过构造函数创建出来的实例会有指针指向原型得到原型的属性和方法。**这样，实例对象就带有构造函数的属性方法和原型的属性方法，然后将需要继承的构造函数的原型指向这个实例，即可拥有这个实例的所有属性方法实现继承**。

看下面演示代码：

    //声明超类，通过构造函数和原型添加有关属性和方法
    function Super(){
        this.property = true;
    }
    Super.prototype.getSuperValue = function() {
        return this.property;
    };

    //声明子类的构造函数
    function SubType() {
        this.subproperty = false;
    }
    //将子类的原型指向超类的实例，得到超类的一切
    SubType.prototype = new Super();
    SubType.prototype.constructor = SubType;
    SubType.prototype.getSubValue = function(){
        return this.subproperty;
    };

    //由子类创建对象，测试是否继承超类方法和属性
    var instance = new SubType();
    console.log(instance.getSuperValue());
    

所有函数的默认原型都是 Object 的实例，因此默认原型都会包含一个内部指针，指向 Object.prototype。

使用 instanceof 和 isPrototypeOf 可以确定原型和实例的关系：

    instance instanceof Object;
    Object.prototype.isPrototypeOf(instance);
    

使用原型链的时候，需要谨慎的定义方法。子类需要重写超类型的某个方法或者扩充，一定要放在替换原型的语句后面，这样才能生效。此外，通过原型链实现继承时，不能使用对象字面量创建原型方法，这样会重写原型链：

    ......
    SubType.prototype = new Super();
    SubType.prototype = {
        ....
    };
    

之前文章里面介绍过了，这样会更换指针指向新对象，从而重写了原型链。

原型链的继承方法是有缺陷的，主要有两个问题：

1，来自包含引用类型值的原型，会被所有实例共享。

前面文章介绍过包含引用类型值的原型属性会被所有实例共享，一个实例修改，其他实例会随之改变，因此需要在构造函数中定义属性。而原型链继承的时候，无论超类中属性是在构造函数还是原型中定义，全部都变成了实例对象被子类继承，从而对子类的实例产生影响。

2，创建子类型的实例时，不能向超类型的构造函数中传递参数。

原型链的继承，直接将子类原型指向超类的实例，这时候可以向超类传递参数。但是当子类创建实例的时候，只能向子类的构造函数传递参数，而不能向超类的构造函数传递参数。

因此实际应用中，很少单独使用原型链。

### 借用构造函数

这种方法思想很简单，即在子类型构造函数的内部调用超类型构造函数。因为函数只不过是在特定环境中执行代码的对象，因此通过使用 apply() 和 call() 方法也可以在将来新创建的对象上执行构造函数：

    function SuperType(){
        this.colors = ["red", "blue", "green"];
    }

    function SubType(){
        SuperType.call(this);
    }

    var instance1 = new SubType();
    instance1.colors.push("black");
    console.log(instance1.colors);

    var instance2 = new SubType();
    console.log(instance2.colors);
    

结果如下：

![借用构造函数继承](http://jiangshui.b0.upaiyun.com/blog/2014/06/exten0.png)

这样同样继承了超类的引用类型值，但是子类的不同实例之间不会相互影响，因为创建超类属性值等，是在子类的环境下执行的。**同时，这样还可以在继承中向超类传递参数**。

借用构造函数继承也不是完美的，继承依赖构造函数，方法都是在构造函数中定义的话，函数就无法复用造成浪费。而且在超类的原型中定义的方法，对子类而言也是不可见的，结果所有的类型最后都只能使用构造函数模式。

因此实际应用中，也很少单独使用借用构造函数。

### 组合继承

组合继承就是将原型链和借用构造函数的方法组合到一起，从而组合使用两者的长处，解决缺陷。思路也很简单：使用原型链实现对圆形属性和方法的继承，而通过借用构造函数来实习对实例属性的继承。

    function SuperType(name){
        this.name  = name;
        this.colors = ["red", "blue", "green"];
    }

    SuperType.prototype.sayName = function(){
        console.log(this.name);
    };

    function SubType(name, age){
        SuperType.call(this, name);
        this.age = age;
    }

    SubType.prototype = new SuperType();

    SubType.prototype.sayAge = function(){
        console.log(this.age);
    };

    var instance1 = new SubType("Jiangshui",22);
    instance1.colors.push("black");
    console.log(instance1.colors);
    instance1.sayName();
    instance1.sayAge();

    var instance2 = new SubType("Dazheng",22);
    console.log(instance2.colors);
    instance2.sayName();
    instance2.sayAge();

![组合继承结果](http://jiangshui.b0.upaiyun.com/blog/2014/06/extend1.png)

组合继承避免了原型链和借用构造函数的缺陷，融合了它们的有点，成为了 JavaScript 中最常用的继承模式。