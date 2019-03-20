---
layout: post
title: Socket.IO实现WebSocket的client端两个小坑
category: 技术
tags: JavaScript, Socket.IO, WebSocket
description: 记录使用Socket.IO实现websocket
---


### 使用Socket.IO实现websocket时，遇到两个小坑，记录如下：

1. Socket.IO无法创建一个可使用（js原生WebSocket方法）连接的websocket，

```
<!DOCTYPE html>
<html>
<head>
    <script>
        var socketClient = new WebSocket("ws://localhost:9999");
    </script>
</head>
<body>
</body>
</html>
```
会有如下报错：
**WebSocket connection to 'ws://localhost:9999/' failed: Connection closed before receiving a handshake response**

原因是“socket.io不是 websocket 组件。尽管socket.io确实在可能的时候使用WebSocket传输，它为每个包增加了一些metadata：包类型（packet type）、namespace以及当需要确认消息时的ack id。这也是为什么一个WebSocket客户端无法成功连接到Socket.io服务端，同样一个socket.io客户端也无法连接到WebServer服务端。”

Socket.IO官网原文（https://socket.io/docs/#What-Socket-IO-is-not）：

> What Socket.IO is not
Socket.IO is NOT a WebSocket implementation. Although Socket.IO indeed uses WebSocket as a transport when possible, it adds some metadata to each packet: the packet type, the namespace and the ack id when a message acknowledgement is needed. That is why a WebSocket client will not be able to successfully connect to a Socket.IO server, and a Socket.IO client will not be able to connect to a WebSocket server either. Please see the protocol specification here.

2. js中引入socket.io.js路径问题

根据官网给出的code，要想连接socket.io建立的server，需要在client端引入socket.io.js，此文件可由server端代码自动生成，也可以通过cdn引入。

```
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io('http://localhost');
  socket.on('news', function (data) {
    console.log(data);
    socket.emit('my other event', { my: 'data' });
  });
</script>
```

但以上代码会报错
**GET file:///socket.io/socket.io.js net::ERR_FILE_NOT_FOUND**

此时，需要在把index.html与实现server的代码放在同一文件路径的前提下，将<script>的src路径补全主机信息

```
<script src="http://localhost:9999/socket.io/socket.io.js"></script>
```

### OVER

以下为Socket.IO官网有关其是什么的解释，所以大部分情况下官方文档才是第一选择。

> 什么是Socket.IO
Socket.IO是一个library，支持浏览器和服务器之间实时、双向以及基于事件的通信。
Socket.IO的构成包括：
一个Node.js实现的server
一个运行于浏览器的 js客户端库（同样可以运行在Node.js中）






