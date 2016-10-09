---
layout: post
title: "Node.js--Stream模块"
categories: Node.js
tags: Javascript IO流
---

* content
{:toc}

> 以下摘自@阮一峰的文章，版权归原作者所有

使用 `stream` 模块时，必须引入相应的包：

```js
    var stream = require('stream');
```



Stream接口分为三类：

* 可读数据流接口，用于读取数据。
* 可写数据流接口，用于写入数据。
* 双向数据流接口，用于读取和写入数据，比如 `Node` 的 `tcp sockets`、`zlib`、`crypto` 都部署了这个接口。

## 可读数据流

“可读数据流”有两种状态：流动态和暂停态。处于流动态时，数据会尽快地从数据源导向用户的程序；处于暂停态时，必须显式调用 `stream.read()`等指令，“可读数据流”才会释放数据。刚刚新建的时候，“可读数据流”处于暂停态。

三种方法可以让暂停态转为流动态：

* 添加 `data` 事件的监听函数
* 调用 `resume` 方法
* 调用 `pipe` 方法将数据送往一个可写数据流

如果转为流动态时，没有 `data` 事件的监听函数，也没有 `pipe` 方法的目的地，那么数据将遗失。

以下两种方法可以让流动态转为暂停态：

* 不存在 `pipe` 方法的目的地时，调用 `pause` 方法
* 存在 `pipe` 方法的目的地时，移除所有 `data` 事件的监听函数，并且调用 `unpipe` 方法，移除所有 `pipe` 方法的目的地

注意，只移除 `data` 事件的监听函数，并不会自动引发数据流进入“暂停态”。另外，存在 `pipe` 方法的目的地时，调用 `pause` 方法，并不能保证数据流总是处于暂停态，一旦那些目的地发出数据请求，数据流有可能会继续提供数据。

每当系统有新的数据，该接口可以监听到 `data` 事件，从而回调函数。

### 方法

#### read()

`read` 方法从系统缓存读取并返回数据。如果读不到数据，则返回 `null`。

该方法可以接受一个整数作为参数，表示所要读取数据的数量，然后会返回该数量的数据。如果读不到足够数量的数据，返回 `null`。如果不提供这个参数，默认返回系统缓存之中的所有数据。

只在“暂停态”时，该方法才有必要手动调用。“流动态”时，该方法是自动调用的，直到系统缓存之中的数据被读光。

```js
    var readable = getReadableStreamSomehow();
    readable.on('readable', function() {
      var chunk;
      while (null !== (chunk = readable.read())) {
        console.log('got %d bytes of data', chunk.length);
      }
    });
```

如果该方法返回一个数据块，那么它就触发了 `data` 事件。

#### setEncoding

调用该方法，会使得数据流返回指定编码的字符串，而不是缓存之中的二进制对象。比如，调用 `setEncoding('utf8')`，数据流会返回 `UTF-8` 字符串，调用 `setEncoding('hex')`，数据流会返回16进制的字符串。

该方法会正确处理多字节的字符，而缓存的方法 `buf.toString(encoding)`不会。所以如果想要从数据流读取字符串，应该总是使用该方法。

```js
    var readable = getReadableStreamSomehow();
    readable.setEncoding('utf8');
    readable.on('data', function(chunk) {
      assert.equal(typeof chunk, 'string');
      console.log('got %d characters of string data', chunk.length);
    });
```

#### resume()

`resume` 方法会使得“可读数据流”继续释放 `data` 事件，即转为流动态。

```js
    var readable = getReadableStreamSomehow();
    readable.resume();
    readable.on('end', function(chunk) {
      console.log('数据流到达尾部，未读取任务数据');
    });
```

上面代码中，调用 `resume` 方法使得数据流进入流动态，只定义 `end` 事件的监听函数，不定义 `data` 事件的监听函数，表示不从数据流读取任何数据，只监听数据流到达尾部。

#### pause()

`pause` 方法使得流动态的数据流，停止释放 `data` 事件，转而进入暂停态。任何此时已经可以读到的数据，都将停留在系统缓存。

```js
    var readable = getReadableStreamSomehow();
    readable.on('data', function(chunk) {
      console.log('读取%d字节的数据', chunk.length);
      readable.pause();
      console.log('接下来的1秒内不读取数据');
      setTimeout(function() {
        console.log('数据恢复读取');
        readable.resume();
      }, 1000);
    });
```

#### isPaused()

该方法返回一个布尔值，表示“可读数据流”被客户端手动暂停（即调用了 `pause`方法），目前还没有调用 `resume` 方法。

```js
    var readable = new stream.Readable

    readable.isPaused() // === false
    readable.pause()
    readable.isPaused() // === true
    readable.resume()
    readable.isPaused() // === false
```

#### pipe()

`pipe` 方法是自动传送数据的机制，就像管道一样。它从“可读数据流”读出所有数据，将其写出指定的目的地。整个过程是自动的。

```js
    src.pipe(dst)
```

`pipe` 方法必须在可读数据流上调用，它的参数必须是可写数据流。

```js
    var fs = require('fs');
    var readableStream = fs.createReadStream('file1.txt');
    var writableStream = fs.createWriteStream('file2.txt');

    readableStream.pipe(writableStream);
```

上面代码使用 `pipe` 方法，将 `file1` 的内容写入 `file2`。整个过程由 `pipe` 方法管理，不用手动干预，所以可以将传送数据写得很简洁。

`pipe` 方法返回目的地的数据流，因此可以使用链式写法，将多个数据流操作连在一起。

```js
    a.pipe(b).pipe(c).pipe(d)
    // 等同于
    a.pipe(b);
    b.pipe(c);
    c.pipe(d);
```

当来源地的数据流读取完成，默认会调用目的地的 `end` 方法，就不再能够写入。对pipe方法传入第二个参数 `{ end: false }`，可以让目的地的数据流保持打开。

```js
    reader.pipe(writer, { end: false });
    reader.on('end', function() {
      writer.end('Goodbye\n');
    });
```

#### unpipe()

该方法移除 `pipe` 方法指定的数据流目的地。如果没有参数，则移除所有的 `pipe` 方法目的地。如果有参数，则移除该参数指定的目的地。如果没有匹配参数的目的地，则不会产生任何效果。

```js
    var readable = getReadableStreamSomehow();
    var writable = fs.createWriteStream('file.txt');
    readable.pipe(writable);
    setTimeout(function() {
      console.log('停止写入file.txt');
      readable.unpipe(writable);
      console.log('手动关闭file.txt的写入数据流');
      writable.end();
    }, 1000);
```

上面代码写入 `file.txt` 的时间，只有1秒钟，然后就停止写入。

### 事件

#### readable

`readable` 事件在数据流能够向外提供数据时触发。

```js
    var readable = getReadableStreamSomehow();
    readable.on('readable', function() {
      // there is some data to read now
    });
```

下面是一个例子：

```js
    process.stdin.on('readable', function () {
      var buf = process.stdin.read();
      console.dir(buf);
    });
```

上面代码将标准输入的数据读出。

`read` 方法接受一个整数作为参数，表示以多少个字节为单位进行读取。

```js
    process.stdin.on('readable', function () {
        var buf = process.stdin.read(3);
        console.dir(buf);
    });
```

上面代码将以3个字节为单位进行输出内容。

#### data

对于那些没有显式暂停的数据流，添加 `data` 事件监听函数，会将数据流切换到流动态，尽快向外提供数据。

```js
    var readable = getReadableStreamSomehow();
    readable.on('data', function(chunk) {
      console.log('got %d bytes of data', chunk.length);
    });
```

#### end

无法再读取到数据时，会触发 `end` 事件。也就是说，只有当前数据被完全读取完，才会触发 `end` 事件，比如不停地调用 `read` 方法。

```js
    var readable = getReadableStreamSomehow();
    readable.on('data', function(chunk) {
      console.log('got %d bytes of data', chunk.length);
    });
    readable.on('end', function() {
      console.log('there will be no more data.');
    });
```

#### close 

数据源关闭时，`close` 事件被触发。并不是所有的数据流都支持这个事件。

#### error

当读取数据发生错误时，`error` 事件被触发。

## 可写数据流

### 方法

#### write()

`write` 方法用于向“可写数据流”写入数据。它接受两个参数，一个是写入的内容，可以是字符串，也可以是一个 `stream` 对象（比如可读数据流），另一个是写入完成后的回调函数。

它返回一个布尔值，表示本次数据是否处理完成。如果返回 `true`，就表示可以写入新的数据了。如果等待写入的数据被缓存了，就返回 `false`。不过，在返回 `false` 的情况下，也可以继续传入新的数据等待写入。只是这时，新的数据不会真的写入，只会缓存在内存中。为了避免内存消耗，比较好的做法还是等待该方法返回 `true`，然后再写入。

```js
    var fs = require('fs');
    var ws = fs.createWriteStream('message.txt');

    ws.write('beep ');

    setTimeout(function () {
        ws.end('boop\n');
    }, 1000);
```

上面代码调用 `end` 方法，数据就不再写入了。

#### cork()，uncork()

`cork` 方法可以强制等待写入的数据进入缓存。当调用 `uncork` 方法或 `end` 方法时，缓存的数据就会吐出。

#### setDefaultEncoding()

`setDefaultEncoding` 方法用于将写入的数据编码成新的格式。它返回一个布尔值，表示编码是否成功，如果返回 `false` 就表示编码失败。

#### end()

`end` 方法用于终止“可写数据流”。该方法可以接受三个参数，全部都是可选参数。第一个参数是最后所要写入的数据，可以是字符串，也可以是 `stream` 对象；第二个参数是写入编码；第三个参数是一个回调函数，`finish` 事件触发时，会调用这个回调函数。

```js
    var file = fs.createWriteStream('example.txt');
    file.write('hello, ');
    file.end('world!');
```

上面代码会在数据写入结束时，在尾部写入`“world！”`。

调用 `end` 方法之后，再写入数据会报错。

```js
    var file = fs.createWriteStream('example.txt');
    file.end('world!');
    file.write('hello, '); // 报错
```

### 事件

#### drain事件

`writable.write(chunk)` 返回 `false` 以后，当缓存数据全部写入完成，可以继续写入时，会触发 `drain` 事件。

```js
    function writeOneMillionTimes(writer, data, encoding, callback) {
      var i = 1000000;
      write();
      function write() {
        var ok = true;
        do {
          i -= 1;
          if (i === 0) {
            writer.write(data, encoding, callback);
          } else {
            ok = writer.write(data, encoding);
          }
        } while (i > 0 && ok);
        if (i > 0) {
          writer.once('drain', write);
        }
      }
    }
```

上面代码是一个写入100万次的例子，通过 `drain` 事件得到可以继续写入的通知。

#### finish事件

调用 `end` 方法时，所有缓存的数据释放，触发 `finish` 事件。该事件的回调函数没有参数。

```js
    var writer = getWritableStreamSomehow();
    for (var i = 0; i < 100; i ++) {
      writer.write('hello, #' + i + '!\n');
    }
    writer.end('this is the end\n');
    writer.on('finish', function() {
      console.error('all writes are now complete.');
    });
```

#### pipe事件

“可写数据流”调用 `pipe` 方法，将数据流导向写入目的地时，触发该事件。

该事件的回调函数，接受发出该事件的“可读数据流”对象作为参数。

```js
    var writer = getWritableStreamSomehow();
    var reader = getReadableStreamSomehow();
    writer.on('pipe', function(src) {
      console.error('something is piping into the writer');
      assert.equal(src, reader);
    });
    reader.pipe(writer);
```

#### error事件

如果写入数据或 `pipe` 数据时发生错误，就会触发该事件。

该事件的回调函数，接受一个 `Error` 对象作为参数。

## HTTP请求实例

`HTTP` 对象使用 `Stream` 接口，实现网络数据的读写。

```js
    var http = require('http');

    var server = http.createServer(function (req, res) {
      // req is an http.IncomingMessage, which is a Readable Stream
      // res is an http.ServerResponse, which is a Writable Stream

      var body = '';
      // we want to get the data as utf8 strings
      // If you don't set an encoding, then you'll get Buffer objects
      req.setEncoding('utf8');

      // Readable streams emit 'data' events once a listener is added
      req.on('data', function (chunk) {
        body += chunk;
      });

      // the end event tells you that you have entire body
      req.on('end', function () {
        try {
          var data = JSON.parse(body);
        } catch (er) {
          // uh oh!  bad json!
          res.statusCode = 400;
          return res.end('error: ' + er.message);
        }

        // write back something interesting to the user:
        res.write(typeof data);
        res.end();
      });
    });

    server.listen(1337);

    // $ curl localhost:1337 -d '{}'
    // object
    // $ curl localhost:1337 -d '"foo"'
    // string
    // $ curl localhost:1337 -d 'not json'
    // error: Unexpected token o
```

## fs模块实例

`fs` 模块的 `createReadStream` 方法用于新建读取数据流，`createWriteStream` 方法用于新建写入数据流。使用这两个方法，可以做出一个用于文件复制的脚本 `copy.js`。

```js
    // copy.js

    var fs = require('fs');
    console.log(process.argv[2], '->', process.argv[3]);

    var readStream = fs.createReadStream(process.argv[2]);
    var writeStream = fs.createWriteStream(process.argv[3]);

    readStream.on('data', function (chunk) {
      writeStream.write(chunk);
    });

    readStream.on('end', function () {
      writeStream.end();
    });

    readStream.on('error', function (err) {
      console.log("ERROR", err);
    });

    writeStream.on('error', function (err) {
      console.log("ERROR", err);
    });d all your errors, you wouldn't need to use domains.
```

## 参考资料

[stream接口@阮一峰](http://javascript.ruanyifeng.com/nodejs/stream.html)

[Node.js API文档](https://nodejs.org/dist/latest-v6.x/docs/api/stream.html)