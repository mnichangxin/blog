---
layout: post
title: "Node.js--Buffer模块"
categories: Node.js
tags: Javascript IO流
---

这里介绍的只是我觉得 `Node.js` 里文件操作几个有用的 `API` ，详细内容得先从官方文档看起

## Buffer(数据块)

官方文档：[Buffer](https://nodejs.org/dist/latest-v6.x/docs/api/buffer.html)

`Buffer` 将 `JS` 的数据处理能力从字符串扩展到了任意二进制数据

`Buffer` 类的实例类似整形数组，但创建时被确定缓冲区大小且不能调整

`Buffer` 类在 `Node.js` 中是一个全局属性，这意味着使用它时不用像这样引用 `require(buffer).Buffer`

### Buffer.from(), Buffer.alloc(), and Buffer.allocUnsafe()

V6版本以后，出于安全考虑，用以上方法替代了以前的 `new Buffer()` 方法。

* Buffer.from(str[, encoding]) 

接受一个普通字符串，其结果将被转化为二进制。

```js
    var bf = Buffer.from('abcd');
    console.log(bf); //<Buffer 61 62 63 64>
```

或者可以为这个字符串指定输出编码方式

* Buffer.from(buffer) 

复制一个 `Buffer` 

```js
    var bf1 = Buffer.from('abcd');
    var bf2 = Buffer.from(bf1);
    console.log(bf1.toString() == bf2.toString()); //true
```

* Buffer.from(array)

传入一个数组给 `Buffer` ，数组每一项指定编码项

```js
    var bf = Buffer.from([0x62, 0x75, 0x66, 0x65, 0x72]);
    console.log(bf); //<Buffer 62 75 66 65 72>
```

* Buffer.alloc(size[, fill[, encoding]])

开辟 `size` 大小的内存空间，这个内存空间是固定的

```js
    var bf = Buffer.alloc(5);
    console.log(bf); //<Buffer 00 00 00 00 00>
```

* Buffer.allocUnsafe(size[, fill[, encoding]])

虽然也有泄露内存中敏感信息的可能，但语义上非常明确。这个我不是很明白，可以看这篇文章，[网页链接](http://www.thinksaas.cn/topics/0/501/501094.html)

* buf[index]

可以访问或者指定每一个 `Buffer` 项的内容，请看示例：

```js
    var str = 'Node.js';
    var bf = Buffer.alloc(str.length);
    for (var i = 0; i < str.length; ++i) {
        bf[i] = str.charCodeAt(i);
    }
    console.log(bf.toString()); //Node.js
```

* buf.compare(target[, targetStart[, targetEnd[, sourceStart[, sourceEnd]]]])

这个就是比较长度嘛，`0` 表示 `buf` 和 `target` 相等，`-1` 表示 `target` 的长度小于 `buf`, `1` 表示 `target` 长度大于 `buf`

```js
    var buf1 = Buffer.from('ABC');
    var buf2 = Buffer.from('BCD');
    var buf3 = Buffer.from('ABCD');

    console.log(buf1.compare(buf1)); //0
    console.log(buf1.compare(buf2)); //-1
    console.log(buf1.compare(buf3)); //1
    console.log(buf2.compare(buf1)); //1
    console.log(buf2.compare(buf3)); //1

    [buf1, buf2, buf3].sort(Buffer.compare);// produces sort order [buf1, buf3, buf2]
```

* buf.copy(targetBuffer[, targetStart[, sourceStart[, sourceEnd]]])

这个很有用，拷贝另一个 `buf` ，并能指定相应拷贝项

```js
    var buf1 = Buffer.allocUnsafe(26);
    var buf2 = Buffer.allocUnsafe(26).fill('!');

    for (var i = 0 ; i < 26 ; i++) {
      buf1[i] = i + 97; // 97 is ASCII a
    }

    buf1.copy(buf2, 8, 16, 20);

    console.log(buf2.toString('ascii', 0, 25)); //!!!!!!!!qrst!!!!!!!!!!!!!
```

* buf.fill(value[, offset[, end]][, encoding])

相当于填充相应的 `buf` 项吧：

```
const b = Buffer.allocUnsafe(5).fill('a');
console.log(b.toString()); //aaaaa
```

* buf.readInt8(offset[, noAssert])

读取一个8位的整形数据，可以指定偏移量

```js
    var buf = Buffer.from([1, -2, 3, 4]);

    buf.readInt8(0); // returns 1
    buf.readInt8(1); // returns -2
```

* buf.slice([start[, end]])

和 `JS` 中的 `slice()` 方法一致

* buf.write(string[, offset[, length]][, encoding])

根据参数 `offset` 偏移量和指定的 `encoding` 编码方式，将参数 `string` 数据写入 `buffer` 。 `offset` 偏移量默认是 `0` ，`encoding` 编码方式默认是 `utf8`。 `length` 长度是将要写入的字符串的 `bytes` 大小。返回 `number` 类型，表示多少8位字节流被写入了。如果 `buffer` 没有足够的空间来放入整个 `string` ，它将只会写入部分的字符串。 `length` 默认是 `buffer.length - offset` 。 这个方法不会出现写入部分字符。

```js
    var buf = Buffer.allocUnsafe(256);
    var len = buf.write('\u00bd + \u00bc = \u00be', 0);
    console.log(`${len} bytes: ${buf.toString('utf8', 0, len)}`); //12 bytes: ½ + ¼ = ¾
```
