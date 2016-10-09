---
layout: post
title: "（转）移动端适配方案"
categories: CSS
tags: 移动Web 
---

> 文章系转载，原文地址请见[网页链接](https://github.com/riskers/blog/issues/18), 作者的另一篇 [文章](https://github.com/riskers/blog/issues/17)

> 推荐一篇文章：`MobileWeb` 适配总结，里面说到的三种布局方法已经说的很详细，还分别做了demo，我就不做了，这里说说三种方案的原理以及我使用中的感受，希望各为互补，大家理解是最重要的。

之前做过 `PC` 页面的人聊的最多的就是『兼容』，这是因为浏览器之间的差异引起的，不再多说。而移动端是基本没有『兼容』的问题的，全是 `CSS3` ，简直不要太开心。可是『适配』问题随之而来。

什么是『适配』？做PC页面的时候，我们按照设计图的尺寸来就好，这个侧边栏200px，那个按钮50px的。可是，当我们开始做移动端页面的时候，设计师给了一份宽度为640px的设计图。那么，我们把这份设计图实现在各个手机上的过程就是『适配』。

那么，我们怎么开始呢？目前有三种方法：

* 固定高度，宽度自适应

* 固定宽度，viewport缩放

* rem做宽度，viewport缩放

这三种方法的核心都是视口的确定，现在以实现这个设计图为例说明。

---

# 固定高度，宽度自适应

[demo](http://www.meow.re/demo/screen-adaptation-in-mobileweb/app-fixed-height.html)

这也是目前使用最多的方法，垂直方向用定值，水平方向用百分比、定值、flex都行。[腾讯](http://xw.qq.com/index.htm)、[京东](http://m.jd.com/)、[百度](https://www.baidu.com/)、[天猫](https://www.tmall.com/)、[亚马逊](http://www.amazon.cn/)的首页都是使用的这种方法。

随着屏幕宽度变化，页面也会跟着变化，效果就和PC页面的流体布局差不多，在哪个宽度需要调整的时候使用响应式布局调调就行（比如[网易新闻](http://news.163.com/mobile/)），这样就实现了『适配』。

## 原理

这种方法使用了完美视口：

```html
    <meta name="viewport" content="width=device-width,initial-scale=1">
```

这样设置之后，我们就可以不用管手机屏幕的尺寸进行开发了。

---

# 固定宽度，viewport缩放

[demo](http://www.meow.re/demo/screen-adaptation-in-mobileweb/app-fixed-width.html)

设计图、页面宽度、viewport width使用一个宽度，浏览器帮我们完成缩放。单位使用px即可。

目前已知 [荔枝FM](http://m.lizhi.fm/)、[网易新闻](http://c.3g.163.com/CreditMarket/default.html)在使用这种方法。有兴趣的同学可以看看是怎么做的。

## 原理

这种方法需要根据屏幕宽度来动态生成 `viewport` ，生成的 `viewport` 基本是这样：

```html
    <meta name="viewport" content="width=640,initial-scale=0.5,maximum-scale=0.5,minimum-scale=0.5,user-scalable=no">
```

生成的 `viewport` 告诉浏览器网页的布局视口使用 640px，然后把页面缩放成50%，这是绝对的等比例缩放。图片、文字等等所有元素都被缩放在手机屏幕中。

---

# rem做宽度，viewport缩放

[demo](http://www.meow.re/demo/screen-adaptation-in-mobileweb/app-rem.html)

这也是 [淘宝](https://m.taobao.com/) 使用的方案，根据屏幕宽度设定 `rem` 值，需要适配的元素都使用 `rem` 为单位，不需要适配的元素还是使用 `px` 为单位。

具体使用方法见 [使用Flexible实现手淘H5页面的终端适配](https://github.com/amfe/article/issues/17)

上文提供了 `sass` 和 `postcss` 的px2rem转换方法，我这里给出 `less` 的px2rem。因为 `less` 不支持函数，所以需要安装插件 [less-plugin-functions](https://github.com/seven-phases-max/less-plugin-functions) ，然后就简单了：

```css
    .function {
        .px2rem(@px,@base:72px) {
            return: @px / @base * 1rem;
        }    
    }
```

这样使用：

使用这个方法的库：

* [lib-flexible](https://github.com/amfe/lib-flexible)

* [hostcss](https://github.com/imochen/hotcss)

## 原理

实际上做了这几件事情：

1. 动态生成 viewport
2. 屏幕宽度设置 rem的大小，即给<html>设置font-size
3. 根据设备像素比（window.devicePixelRatio）给<html>设置data-dpr

这么设置的好处是可以让不同设备的rem或px都显示一样的长度。

## 设置rem

设置 `rem` 的意义在于得到一个与屏幕宽度相关的单位，本来 `vw` 是最合适的，但是因为兼容性的问题，只能使用 `rem` 来做。这样，让不同设备的`rem` 显示一样的长度 。

> `vw` 是 `CSS3` 引入的单位，`1vw = 1%` 窗口宽度

## 设置 viewport 缩放 和 data-dpr

这两个设置其实是干的一件事，就是适配高密度屏幕手机的 `px` 单位。

```css
    .a{
      font-size:12px;
    }
    [data-dpr="2"] .a{
      font-size: 24px;
    }
    [data-dpr="3"] .a{
      font-size: 36px;
    }
```

而缩放是动态的，这样，不同设备下的px显示一样的长度。

之前说过 `CSS` 像素和物理像素与缩放、`dpr` 都有关系，这里说明：

> 在普通手机上，.a字体设置为12px；

> 在 `dpr` 是2的手机上，[data-dpr="2"] .a字体为24px，又因为页面缩放50%，字体为还是12px

---

# 总结

坦白说，我不觉得第一种方案能就做『适配』方案，因为太笨了，只能做一些列表等简单排列的样式。

但是一些复杂的活动页的布局，用它可能就力不从心了。

其他文章

* [移动端高清、多屏适配方案](http://div.io/topic/1092)
 
* [从网易与淘宝的font-size思考前端设计稿与工作流](http://www.cnblogs.com/lyzg/p/4877277.html)
 
* [百度方案](http://js8.in/2015/12/12/%E6%89%8B%E6%9C%BA%E7%99%BE%E5%BA%A6%E7%A7%BB%E5%8A%A8%E9%80%82%E9%85%8D%E5%88%87%E5%9B%BE%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88%E4%BB%8B%E7%BB%8D/)
 
* [移动端自适应方案](http://f2e.souche.com/blog/yi-dong-duan-zi-gua-ying-fang-an/) 介绍了 flex 布局和rem方案

