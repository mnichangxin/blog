---
layout: post
title: "Websocket API"
category: Network
tag: Websocket Comet
---

通常的轮询和流都是基于单向的请求连接， `HTML5` 的 `Websocket` 实现了数据的双向传递。下面是关于这方面的摘要：

# 实现Websocket

如果你按照下列步骤进行操作，则实现此数据交换新技术将非常简单：

1.  使用一个客户端浏览器来实现 `WebSocket` 协议
2.  在网页中编写代码来创建客户端 `Websocket`
3.  在 Web 服务器上编写代码来通过 `Websocket` 响应客户端请求

## 目前为止支持的浏览器

* IE10+

* Firefox47+

* Chrome31+

* Opera38+

* Android4.4+


## 客户端示例代码

```js
    var host = "ws://sample-host/echo";
     try {
        socket = new WebSocket(host);

        socket.onopen = function (openEvent) {
           document.getElementById("serverStatus").innerHTML = 
              'WebSocket Status:: Socket Open';
        };

     socket.onmessage = function (messageEvent) {

        if (messageEvent.data instanceof Blob) {
        var destinationCanvas = document.getElementById('destination');
        var destinationContext = destinationCanvas.getContext('2d');
        var image = new Image();
        image.onload = function () {
           destinationContext.clearRect(0, 0, 
              destinationCanvas.width, destinationCanvas.height);
           destinationContext.drawImage(image, 0, 0);
        }
        image.src = URL.createObjectURL(messageEvent.data);
     } else {
        document.getElementById("serverResponse").innerHTML = 
           'Server Reply:: ' + messageEvent.data;
        }
     };

     socket.onerror = function (errorEvent) {
        document.getElementById("serverStatus").innerHTML = 
          'WebSocket Status:: Error was reported';
        };

     socket.onclose = function (closeEvent) {
        document.getElementById("serverStatus").innerHTML = 
          'WebSocket Status:: Socket Closed';
        };
     }
      catch (exception) { if (window.console) console.log(exception); }
     }

     function sendTextMessage() {

        if (socket.readyState != WebSocket.OPEN) return;

        var e = document.getElementById("textmessage");
        socket.send(e.value);
     }

     function sendBinaryMessage() {
        if (socket.readyState != WebSocket.OPEN) return;

        var sourceCanvas = document.getElementById('source');

        socket.send(sourceCanvas.msToBlob());
     }   
```

前面的代码假设你有 `serverStatus` 、`destination` 、`serverResponse` 、`textmessage` 和 `serverData` 作为你的网页中带 `ID` 的元素。如果 `F12` 开发人员工具正在运行，捕获结果将显示在控制台窗口中。要发送文本消息数据，请使用以下类型的代码：

```js
    var e = document.getElementById("msgText");
    socket.send(e.value);
```

上面的代码示例假设你有要使用网页中带 `ID` 的 `msgText` 元素发送的消息文本。同样，你可以使用 `onmessage` 事件检测新消息或使用 `send` 方法发送消息到服务器。`send` 方法可用于发送文本、二进制数组或 `Blob` 数据

## 编写 WebSocket 服务器代码

处理套接字的服务器代码能够以任何服务器语言编写。无论你选择哪种语言，都必须编写代码以接受 `WebSocket` 请求并相应地处理它们

## Websocket编程接口

`WebSocket` 提供一组可用于 `WebSocket` 编程的对象、方法和属性：

* Websocket

```js
    var mySocket = new WebSocket(URL, [protocols]);
```

|Method|Description|
|:-----|:----------|
|close|Closes a WebSocket connection.|
|send|Sends data to the server using a WebSocket connection.|

* binaryType

```js
    object.binaryType = binaryType
    binaryType = object.binaryType
```

Property values:

|Value|Condition|
|:----|:--------|
|blob|Represents raw data.|
|arrayBuffer|Represents a binary data buffer.|

* onclose 

* onerror

* onmessage

* onopen

* readtState

以下其中之一的状态：

1. CONNECTING (0) Default
2. OPEN (1)
3. CLOSING (2)
4. CLOSED (3)

更多关于 `API` 的详细请见 ![Websocket API](https://msdn.microsoft.com/library/hh673567)



