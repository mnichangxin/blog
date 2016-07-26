---
layout: post
title: "Npm package.json文件（摘自@阮一峰的教程）"
categories: Node.js
tags: npm package json
---

## 描述性字段

* **name**

* **version**

* **keywords**

* **author**

* **license**

* **repository**

* **homepage**

* **bugs**

## script字段

`scripts` 指定了运行脚本命令的 `npm` 命令行缩写，比如 `start` 指定了运行 `npm run start` 时，所要执行的命令。

下面的设置指定了 `npm run preinstall`、`npm run postinstall`、`npm run start`、`npm run test` 时，所要执行的命令。

```json
    "scripts": {
        "preinstall": "echo here it comes!",
        "postinstall": "echo there it goes!",
        "start": "node index.js",
        "test": "tap test/*.js"
    }
```

## dependencies字段，devDependencies字段

`dependencies` 字段指定了项目运行所依赖的模块，`devDependencies` 指定项目开发所需要的模块。

它们都指向一个对象。该对象的各个成员，分别由模块名和对应的版本要求组成，表示依赖的模块及其版本范围。

```json
    {
      "devDependencies": {
        "browserify": "~13.0.0",
        "karma-browserify": "~5.0.1"
      }
    }
```

对应的版本可以加上各种限定，主要有以下几种：

* 指定版本：比如1.2.2，遵循“大版本.次要版本.小版本”的格式规定，安装时只安装指定版本。
* 波浪号（tilde）+指定版本：比如~1.2.2，表示安装1.2.x的最新版本（不低于1.2.2），但是不安装1.3.x，也就是说安装时不改变大版本号和次要版本号。
* 插入号（caret）+指定版本：比如ˆ1.2.2，表示安装1.x.x的最新版本（不低于1.2.2），但是不安装2.x.x，也就是说安装时不改变大版本号。需要注意的是，如果大版本号为0，则插入号的行为与波浪号相同，这是因为此时处于开发阶段，即使是次要版本号变动，也可能带来程序的不兼容。
* latest：安装最新版本。

`package.json` 文件可以手工编写，也可以使用 `npm init` 命令自动生成。

```ruby
    $ npm init
```

这个命令采用互动方式，要求用户回答一些问题，然后在当前目录生成一个基本的 `package.json` 文件。所有问题之中，只有项目名称（name）和项目版本（version）是必填的，其他都是选填的。

有了 `package.json` 文件，直接使用 `npm install` 命令，就会在当前目录中安装所需要的模块。

```ruby
    $ npm install
```

如果一个模块不在 `package.json` 文件之中，可以单独安装这个模块，并使用相应的参数，将其写入 `package.json` 文件之中。

```ruby
    $ npm install express --save
    $ npm install express --save-dev
```

上面代码表示单独安装 `express` 模块，`--save` 参数表示将该模块写入`dependencies` 属性，`--save-dev` 表示将该模块写入 `devDependencies` 属性。

# peerDependencies字段

有时，你的项目和所依赖的模块，都会同时依赖另一个模块，但是所依赖的版本不一样。比如，你的项目依赖A模块和B模块的1.0版，而A模块本身又依赖B模块的2.0版。

大多数情况下，这不构成问题，B模块的两个版本可以并存，同时运行。但是，有一种情况，会出现问题，就是这种依赖关系将暴露给用户。

最典型的场景就是插件，比如A模块是B模块的插件。用户安装的B模块是1.0版本，但是A插件只能和2.0版本的B模块一起使用。这时，用户要是将1.0版本的B的实例传给A，就会出现问题。因此，需要一种机制，在模板安装的时候提醒用户，如果A和B一起安装，那么B必须是2.0模块。

`peerDependencies` 字段，就是用来供插件指定其所需要的主工具的版本。

```json
    {
      "name": "chai-as-promised",
      "peerDependencies": {
        "chai": "1.x"
      }
    }
```

上面代码指定，安装 `chai-as-promised` 模块时，主程序 `chai` 必须一起安装，而且 `chai` 的版本必须是 `1.x`。如果你的项目指定的依赖是 `chai` 的 `2.0` 版本，就会报错。

注意，从 `npm 3.0` 版开始，`peerDependencies` 不再会默认安装了。

## bin字段

`bin` 项用来指定各个内部命令对应的可执行文件的位置。

```json
    "bin": {
      "someTool": "./bin/someTool.js"
    }
```

上面代码指定，`someTool` 命令对应的可执行文件为 `bin` 子目录下的 `someTool.js`。`Npm` 会寻找这个文件，在 `node_modules/.bin/`目录下建立符号链接。在上面的例子中，`someTool.js` 会建立符号链接 `npm_modules/.bin/someTool`。由于 `node_modules/.bin/` 目录会在运行时加入系统的 `PATH` 变量，因此在运行 `npm` 时，就可以不带路径，直接通过命令来调用这些脚本。

因此，像下面这样的写法可以采用简写。

```json
    "scripts": {  
      "start": "./node_modules/someTool/someTool.js build"
    }

    // 简写为
    "scripts": {  
      "start": "someTool build"
    }
```

所有 `node_modules/.bin/` 目录下的命令，都可以用 `npm run [命令]` 的格式运行。在命令行下，键入 `npm run` ，然后按 `tab` 键，就会显示所有可以使用的命令。

## main字段

`main` 字段指定了加载该模块时的入门文件，默认是模块根目录下面的 `index.js`。

## config字段

`config` 字段用于向环境变量输出值。

下面是一个 `package.json` 文件。

```json
    {
      "name" : "foo",
      "config" : { "port" : "8080" },
      "scripts" : { "start" : "node server.js" }
    }
```

然后，在 `server.js` 脚本就可以引用 `config` 字段的值。

用户可以改变这个值。

```ruby
    $ npm config set foo:port 80
```

## 参考资料

[package.json文件](http://javascript.ruanyifeng.com/nodejs/packagejson.html)

[npm官方文档](https://docs.npmjs.com/getting-started/using-a-package.json)









