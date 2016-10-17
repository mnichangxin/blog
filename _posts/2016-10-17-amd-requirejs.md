---
layout: post
title: "Require.js小记"
categories: JavaScript
tags: AMD Require.js
---

* content
{:toc}


1. baseUrl

`RequireJS` 的目标是鼓励代码的模块化，它使用了不同于传统 `<script>`标签的脚本加载步骤。可以用它来加速、优化代码，但其主要目的还是为了代码的模块化。它鼓励在使用脚本时以 `module ID` 替代 `URL` 地址。

`RequireJS` 以一个相对于 `baseUrl` 的地址来加载所有的代码。页面顶层 `<script>`标签含有一个特殊的属性 `data-main` ，`require.js` 使用它来启动脚本加载过程，而 `baseUrl` 一般设置到与该属性相一致的目录。下列示例中展示了 `baseUrl` 的设置：

```html
    <script data-main="scripts/main.js" src="scripts/require.js"></script>
```

`baseUrl` 亦可通过 `RequireJS config` 手动设置。如果没有显式指定 `config` 及 `data-main` ，则默认的 `baseUrl` 为包含 `RequireJS` 的那个 `HTML` 页面的所属目录。

2. data-main入口点

require.js 在加载的时候会检察data-main 属性:

```html
    <script data-main="scripts/main" src="scripts/require.js"></script>
```

3. 模块定义

* 简单定义：

如果一个模块仅含值对，没有任何依赖，则在 `define()` 中定义这些值对就好了：

```js
    define({
        color: "black",
        size: "unisize"
    });
```

* 函数式定义：

如果一个模块没有任何依赖，但需要一个做 `setup` 工作的函数，则在 `define()`中定义该函数，并将其传给 `define()` ：

```js
    define(function () {
    //Do setup work here

        return {
            color: "black",
            size: "unisize"
        }
    });
```

* 存在依赖的函数定义：

如果模块存在依赖：则第一个参数是依赖的名称数组；第二个参数是函数，在模块的所有依赖加载完毕后，该函数会被调用来定义该模块，因此该模块应该返回一个定义了本模块的 `object` 。依赖关系会以参数的形式注入到该函数上，参数列表与依赖名称列表一一对应。

```js
    define(["./cart", "./inventory"], function(cart, inventory) {
            //return an object to define the "my/shirt" module.
            return {
                color: "blue",
                size: "large",
                addToCart: function() {
                    inventory.decrement(this);
                    cart.add(this);
                }
            }
        }
    );
```


