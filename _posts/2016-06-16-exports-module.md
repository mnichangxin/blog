---
layout: post
title: "exports和module.exports的区别"
categories: Node.js
tags: Javascript CommonJs
---

* content
{:toc}

`Node.js` 中的 `exports` 和 `module` 都向外界暴露了接口，关于两者的区别，[Zihua Li](http://zihua.li/2012/03/use-module-exports-or-exports-in-node/) 的 文章给出了让人信服的解释

`Node.js` 的源码如下：




```js
    // file path: node/lib/module.js

    // ...
    global.require = require;
    global.exports = self.exports;
    global.__filename = filename;
    global.__dirname = dirname;
    global.module = self;

    return runInThisContext(content, filename, true);
    // ...
```

`exports` 是对 `self.exports` 的引用，如果对其赋值，`exports` 不再是 `self.exports` 的引用。而 `module` 引用的是 `self` ，那么对 `module.export` 赋值就没有问题了

测试两段代码：

```js
    //foo.js
    var foo = function(name) {
        console.log(name);
    }    
    exports.foo = foo;

    //index.js
    var obj = require('./foo');
    obj.foo('xiaoli');
```

```js
    //foo.js
    var foo = function(name) {
        console.log(name);   
    }
    module.exports = foo;

    //index.js
    var obj = require('./foo');
    obj('xiaowang');
``` 

输出如下：

```ruby
    xiaoli //代码1
    xiaowang  //代码2
```

代码验证了以上结论：代码1中 `exports` 添加了一个 `foo` 属性，代码2则直接把函数赋给了 `exports` (因为 `exports` 引用着 `module.exports`)。所以 `require` 外部模块的时候，一个需要属性访问，另一个则不需要

小技巧：

如果不给 `exports` 本身赋值，就用：

```js
    exports.property = obj;
```

如果赋值，就用：

```js
    exports = module.exports = obj;
```




