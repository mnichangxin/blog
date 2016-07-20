---
layout: post
title: "Node.js事件循环"
category
---

## 事件

`Node.js` 所有的异步 `I/O` 操作在完成时都会发送一个事件到事件队列。在开发者看来，事
件由 `EventEmitter` 对象提供。下面我们用一个简单的例子说明 `EventEmitter` 的用法：

```js
	//event.js
	var EventEmitter = require('events').EventEmitter;
	var event = new EventEmitter();

	event.on('some_event', function() {
		console.log('some_event occured.');
	});

	setTimeout(function() {
		event.emit('some_event');
	}, 1000);
```

运行这段代码， `1` 秒后控制台输出了 `some_event occured.`。其原理是 `event` 对象注册了事件 `some_event` 的一个监听器，然后我们通过 `setTimeout` 在 `1000` 毫秒以后向 `event` 对象发送事件 `some_event` ，此时会调用 `some_event` 的监听器。

## Node.js 的事件循环机制

`Node.js` 在什么时候会进入事件循环呢？答案是 `Node.js` 程序由事件循环开始，到事件循环结束，所有的逻辑都是事件的回调函数，所以 `Node.js` 始终在事件循环中，程序入口就是事件循环第一个事件的回调函数。事件的回调函数在执行的过程中，可能会发出 `I/O` 请求或直接发射（`emit`）事件，执行完毕后再返回事件循环，事件循环会检查事件队列中有没有未处理的事件，直到程序结束。下图说明了事件循环的原理。与其他语言不同的是，`Node.js` 没有显式的事件循环，类似 `Ruby` 的 `EventMachine::run()` 的函数在 `Node.js` 中是不存在的。`Node.js` 的事件循环对开发者不可见，由 `libev` 库实现。`libev` 支持多种类型的事件，如 `ev_io` 、 `ev_timer` 、 `ev_signal` 、 `ev_idle` 等，在 `Node.js` 中均被 `EventEmitter` 封装。 `libev` 事件循环的每一次迭代，在 `Node.js` 中就是一次 `Tick` ， `libev` 不断检查是否有活动的、可供检测的事件监听器，直到检测不到时才退出事件循环，进程结束

![](http://7xr2ek.com1.z0.glb.clouddn.com/image/jpg/nodejs-event-loop.png)

## 参考资料

[Node.js开发指南](https://book.douban.com/subject/10789820/)