title: JavaScript 依据 CSS Media Queries 判断设备的方法
id: 78
categories:
  - JavaScript
date: 2013-12-06 09:45:53
tags:
---

能够判断设备的状态是对于 Web APP 的开发来说，是非常重要的。在实际的工作中，往往使用 CSS Media Queries 来判断设备做响应式，而忘记了 JavaScript。JavaScript 判断相关设备并执行不同的功能也是很重要的，本文主要借鉴 [David Walsh](http://davidwalsh.name/) 写的 [Device State Detection with CSS Media Queries and JavaScript](http://davidwalsh.name/device-state-detection-css-media-queries-javascript) 这篇文章中的思路：通过 CSS Media Queries 改变一个类的某个属性值（例如 `z-index`）然后用 JavaScript 读取判断，从而实现 JavaScript 判断设备状态执行不同的代码。

<!-- more -->

## CSS 部分

首先随便新建一个用来做判断的类，然后通过 Media Queries 来对这个类的 `z-index` 属性赋予不同的值。这个类仅作为 JavaScript 读取使用，所以需要将其移出屏幕窗口，让浏览者不可见以免引起意外情况。

作为演示，下面代码设置了四种设备状态：桌面普通版、小屏幕桌面版、平板电脑版和手机版。

    /* default state */
    .state-indicator {
        position: absolute;
        top: -999em;
        left: -999em;

        z-index: 1;
    }

    /* small desktop */
    @media all and (max-width: 1200px) {
        .state-indicator {
            z-index: 2;
        }
    }

    /* tablet */
    @media all and (max-width: 1024px) {
        .state-indicator {
            z-index: 3;
        }
    }

    /* mobile phone */
    @media all and (max-width: 768px) {
        .state-indicator {
            z-index: 4;
        }
    }
    

## JavaScript 判断

CSS 已经就位了，那么就需要用 JavaScript 来生成了一个临时的 DOM 对象，然后为它设置对应的类，然后再读取这个对象的 `z-index` 值。原生的写法如下：

    // Create the state-indicator element
    var indicator = document.createElement('div');
    indicator.className = 'state-indicator';
    document.body.appendChild(indicator);

    // Create a method which returns device state
    function getDeviceState() {
        return parseInt(window.getComputedStyle(indicator).getPropertyValue('z-index'), 10);
    }
    

`getDeviceState()` 函数返回的就是 `z-index` 的值，为了增强一下可读性，可以用 switch 函数来规范输出一下：

    function getDeviceState() {
        switch(parseInt(window.getComputedStyle(indicator).getPropertyValue('z-index'), 10)) {
            case 2:
                return 'small-desktop';
                break;
            case 3:
                return 'tablet';
                break;
            case 4:
                return 'phone';
                break;
            default:
                return 'desktop';
                break;
        }
    }
    

这样，就可以用一下代码来判断设备状态，然后执行相应的 JavaScript 代码：

    if(getDeviceState() == 'tablet') {
        // 平板电脑下执行的 JavaScript 代码
    }
    

这里如果你使用的是 jQuery，直接使用下面代码就可以了：

    $(function(){
        $('body').append('<div class="state-indicator"></div>');

        function getDeviceState() {
            switch(parseInt($('.state-indicator').css('z-index'),10)) {
                case 2:
                    return 'small-desktop';
                    break;
                case 3:
                    return 'tablet';
                    break;
                case 4:
                    return 'phone';
                    break;
                default:
                    return 'desktop';
                    break;
            }
        }

        console.log(getDeviceState());
        $('.state-indicator').remove();
    });

先创建，然后获取，最后删掉这个节点，具体的设备会在你的控制台中输出，[点击这里查看 Demo](http://jsfiddle.net/yujiangshui/6A87y/2/) ，可以试着拖动一下中间的边框，然后点击 `Run`。

## 再增强一下

上面实现了使用 JavaScript 配合 CSS Media Queries 来检测设备状态，在测试的时候拖动窗口大小，CSS Media Queries 也会随着改变，如果需要 JavaScript 也随之改变，需要监听 `resize` 事件，不断的进行判断，这里不再赘述。

本文仅作抛砖引玉之用，提供一个简单的思路，你可以根据自己的需求进一步的扩充，比如增加更多的 CSS 属性来进行更加全面详细的判断等等。更多的细节，可以看下 [Device State Detection with CSS Media Queries and JavaScript](http://davidwalsh.name/device-state-detection-css-media-queries-javascript)