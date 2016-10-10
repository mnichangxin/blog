---
layout: post
title: "JavaScript设计模式系列---（一）单例模式"
categories: JavaScript
tags: JS高级特性 设计模式
---

* content
{:toc}

本系列文章系关于 `JavaScript` 设计模式的介绍，本人也是初步接触设计模式，希望通过这系列的文章一步一步深入地理解这些模式的精粹。文章可能大量引用其他文章或书籍来讲解，笔者水平有限文章，如有纰漏欢迎提出建议。系列文章将连载在我的这个博客上。




# （Singleton）单例模式

> 单例模式是设计模式中最简单的形式之一。这一模式的目的是使得类的一个对象成为系统中的唯一实例。要实现这一点，可以从客户端对其进行实例化开始。因此需要用一种只允许生成对象类的唯一实例的机制，“阻止”所有想要生成对象的访问。使用工厂方法来限制实例化过程。这个方法应该是静态方法（类方法），因为让类的实例去生成另一个唯一实例毫无意义。

## JavaScript中的单例模式

单例模式之所以这么叫，是因为它限制一个类只能有一个实例化对象。经典的实现方式是，创建一个类，这个类包含一个方法，这个方法在没有对象存在的情况下，将会创建一个新的实例对象。如果对象存在，这个方法只是返回这个对象的引用。
单例和静态类不同，因为我们可以退出单例的初始化时间。通常这样做是因为，在初始化的时候需要一些额外的信息，而这些信息在声明的时候无法得知。对于并不知晓对单例模式引用的代码来讲，单例模式没有为它们提供一种方式可以简单的获取单例模式。这是因为，单例模式既不返回对象也不返回类，它只返回一种结构。可以类比闭包中的变量不是闭包-提供闭包的函数域是闭包（绕进去了）。

在JavaScript语言中, 单例服务作为一个从全局空间的代码实现中隔离出来共享的资源空间是为了提供一个单独的函数访问指针。

下面是单例模式的一个例子：

```js
    /* Singleton(单例)模式 */
    var Singleton = (function() {
        //实例保持了Singleton的一个引用
        var instance;
        function init() {
            //Singleton
            //私有方法和变量
            function privateMethod() {
                console.log('I am private');
            }
            var privateVariable = 'I am also private';
            var privateRandomNumber = Math.random();
            return {
                //公有方法和变量
                publicMethod: function() {
                    console.log('The public can see me!');
                },
                publicProperty: 'I am also public',
                getRandomNumber: function() {
                    return privateRandomNumber;
                }
            };
        }
        return  {
            //获取Singleton的实例，如果存在就返回，不存在就创建新实例
            getInstance: function() {
                if (!instance) {
                    instance = init();
                }
                return instance;
            }
        };
    })();
```

这就是单例模式的基本模型，下面用一段代码测试一下：

```js
    var singleA = Singleton.getInstance();
    var singleB = Singleton.getInstance();
    console.log(singleA.getRandomNumber === singleB.getRandomNumber);
```

输出结果是 `true` ，于是验证之前的 `Singleton` 引用是唯一不变的。

## 应用举例

例如，我们要写这样一个弹窗组件：

点击按钮弹窗，再次点击按钮不再弹窗，弹窗相当于一个单例，大于一次的时候仍然是第一次的实例。

想了一下，可以这样实现：

```js
   var createBox = (function() {
    var box;
    return {
        getBox: function() {
            return box || (box = document.body.appendChild(document.createElement('div')));
        }
    };
   })(); 
```

以一个闭包的形式包住，最后返回这个 `box` 。

然后，如果想封装一些属性的话，可以这样实现：

```js
    var createBox = (function() {
        var box,
            initBox = function() {
                //私有属性和方法
                //........

                //共有属性和方法
                return box || (box = document.body.appendChild(document.createElement('div')));
            };
        return {
            getBox: function() {
                return initBox;
            }
        };
    })();
```

再次对代码进行了封装，可用性更强。

（Singleton）单例模式算是设计模式中比较容易理解的模式了，用途也比较宽泛。

下面是我觉得写得不错的参考资料：

# 参考资料

[学用JavaScript设计模式](http://www.oschina.net/translate/learning-javascript-design-patterns)

[JavaScript设计模式深入分析](http://developer.51cto.com/art/201109/288650.htm)

[常用的设计模式](http://blog.jobbole.com/29454/)

[前端攻略--JavaScript设计模式](http://www.cnblogs.com/Darren_code/archive/2011/08/31/JavascripDesignPatterns.html)


