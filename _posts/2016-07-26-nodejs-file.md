---
layout: post
title: "Node.js--File System模块"
categories: Node.js
tags: Javascript File
---

> `fs` 是 `filesystem` 的缩写，该模块提供本地文件的读写能力，基本上是 `POSIX` 文件操作命令的简单包装。但是，这个模块几乎对所有操作提供异步和同步两种操作方式，供开发者选择。

用以下方法时，均需先引用 `fs` 模块：

```js
    var fs = require('fs');
```

## mkdir()

用于新建目录。该方法接受三个参数，第一个是目录名，第二个是可选的权限值，第三个是回调函数。

```js
    fs.mkdir('./test', 0777, function(err) {
        if (err) {
            throw err;
        }
        console.log('创建成功！');
    });
```

## writeFile()

用于写入文件（异步），如果文件不存在则创建然后写入。

```js
    fs.writeFile('./test/test1.txt', 'Hello World!', function(err) {
        if (err) {
            throw err;
        }
        console.log('文件写入成功！');
    });
```

## readFile()

用于读取文件（异步）。第一个参数是文件名，第二个参数是文件编码（可选），第三个参数是回调函数。如果没有指定文件编码，返回的是原始的二进制数据（Buffer对象），这时候需要调用 `toString` 方法，将其转为字符串。

```js
    fs.readFile('./test/test1.txt', 'utf8', function(err, data) {
        if (err) {
            throw err;
        }
        console.log(data);
    });
```

## mkdirSync(), writeFileSync(), readFileSync()

这三个方法是上述三个方法的同步形式。

## readdir()和readdirSync()

用于读取目录，返回一个包含文件和子目录的数组。

```js
    fs.readdir('./', 'utf8', function(err, data) {
        if (err) {
            throw err;
        }
        console.log(data);
    });
```

## stat()和statSync()

`stat` 方法的参数是一个文件或目录，它产生一个对象，该对象包含了该文件或目录的具体信息。我们往往通过该方法，判断正在处理的到底是一个文件，还是一个目录。

```js
    fs.stat('./test/test1.txt', function(err, stats) {
        if (err) {
            throw err;
        }
        console.log(stats);
    });
```

## watch()

监听文件的变动并触发事件。第一个参数是监听的文件夹或文件目录，第二个参数是一个回调函数。

```js
    fs.watch('./test', 'utf8', function(eventType, filename) {
        console.log('eventType: ' + eventType);
        if (filename) {
            console.log('filename: ' + filename);
        }
    });
```

## watchFile()和unwatchFile()

`watchfile` 方法监听一个文件，如果该文件发生变化，就会自动触发回调函数。

```js
    fs.watchFile('./testFile.txt', function (curr, prev) {
      console.log('the current mtime is: ' + curr.mtime);
      console.log('the previous mtime was: ' + prev.mtime);
    });
```

`unwatchFile` 方法用于解除对文件的监听。

**注意：** `fs.watch()` 比 `fs.watchFile()` 和 `fs.unwatch()` 更有效率，必要的时候，可以考虑用 `fs.watch()` 替代 `fs.watchFile()` 和 `fs.unwatchFile()`

# createReaderStream()

`createReadStream` 方法往往用于打开大型的文本文件，创建一个读取操作的数据流。所谓大型文本文件，指的是文本文件的体积很大，读取操作的缓存装不下，只能分成几次发送，每次发送会触发一个 `data` 事件，发送结束会触发 `end` 事件。

```js
    var fs = require('fs');

    function readLines(input, func) {
      var remaining = '';

      input.on('data', function(data) {
        remaining += data;
        var index = remaining.indexOf('\n');
        var last  = 0;
        while (index > -1) {
          var line = remaining.substring(last, index);
          last = index + 1;
          func(line);
          index = remaining.indexOf('\n', last);
        }

        remaining = remaining.substring(last);
      });

      input.on('end', function() {
        if (remaining.length > 0) {
          func(remaining);
        }
      });
    }

    function func(data) {
      console.log('Line: ' + data);
    }

    var input = fs.createReadStream('lines.txt');
    readLines(input, func);

```

## createWriteStream()

`createWriteStream` 方法创建一个写入数据流对象，该对象的 `write` 方法用于写入数据，`end` 方法用于结束写入操作。

```js
    var out = fs.createWriteStream(fileName, { encoding: "utf8" });
    out.write(str);
    out.end();
```

## 参考资料

[Node.js API文档](https://nodejs.org/dist/latest-v6.x/docs/api/fs.html)

[fs模块@阮一峰](http://javascript.ruanyifeng.com/nodejs/fs.html)





