## 一、双向通信
### 1.轮询（polling）
* 每隔一段时间就询问一次，程序每发出一次请求就要新建一个Http请求
* 每次发送请求都会有Http的Header，会很耗流量，也会消耗CPU的利用率
* 轮询的间隔过长，会导致用户不能及时接收到更新的数据；轮询的间隔过短，会导致查询请求过多，增加服务器端的负担
```js
let clockDiv = document.getElementById('clock');
setInterval(function () {
    let xhr = new XMLHttpRequest;
    xhr.open('GET', '/clock', true);
    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4 && xhr.status == 200) {
            console.log(xhr.responseText);
            clockDiv.innerHTML = xhr.responseText;
        }
    }
    xhr.send();
}, 1000); // 或用setTimeOut，保证请求返回顺序
```

### 2.长轮询（long-polling）
> 长轮询意味着浏览器只需启动一个HTTP请求，其连接的服务器会“hold”住此次连接，直到有新消息才返回响应信息并关闭连接
* 优点：比 Polling 做了优化，有较好的时效性
* 缺点：保持连接会消耗资源; 服务器没有返回有效数据，程序超时

**轮询可能在以下3种情况时终止：**
1. **有新数据推送**。当服务器向浏览器推送信息后，应该主动结束程序运行从而让连接断开，这样浏览器才能及时收到数据。 
2. **没有新数据推送**。应该设定一个最长时限，避免WEB服务器超时（**Timeout**），若一直没有新信息，服务器应主动向浏览器发送本次轮询无新信息的正常响应，并断开连接，这也被称为“心跳”信息。
3. **网络故障或异常**。由于网络故障等因素造成的请求超时或出错也可能导致轮询的意外中断，此时浏览器将收到错误信息。
```js
let clockDiv = document.getElementById('clock')
function sendReq() {
    let xhr = new XMLHttpRequest()
    xhr.open('GET', '/clock', true)
    xhr.timeout = 2000 // 超时时间，单位是毫秒
    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4) {
            if (xhr.status == 200) {
                clockDiv.innerHTML = xhr.responseText // 返回成功，处理返回数据
            }
            send() // 不管成功还是失败都会发下一次请求
        }
    }
    xhr.ontimeout = function () { // 请求超时
        send()
    }
    xhr.send()
}
sendReq()
```

### 3.iframe流（streaming）/ 长连接
> 在传统的Htttp请求时（**http/1.0版本**），客户端发出请求，服务端回复后http连接关闭。然后在下一次Http请求时又要建立新的Tcp连接。

> 为了解决这一问题，**Http/1.1**想出了持久化连接。只要任意一端没有明确的提出断开连接，则保持TCP连接状态。通过首部字段**Connection：Keep-Alive**实现。Http/1.1默认为长连接。
* iframe流方式是在页面中插入一个隐藏的iframe，利用其src属性在服务器和客户端之间创建一条长连接
* 优点：消息能够实时到达；浏览器兼容好
* 缺点：服务器维护一个长连接会增加开销；IE、chrome、Firefox会显示加载没有完成，图标会不停旋转
* 客户端只请求一次，然而服务端却是源源不断向客户端发送数据，这样服务器维护一个长连接会增加开销

<br>

## 二、WebSocket
![life circle](/images/websocket.png)
#### 1.简介
> WebSocket是HTML5一种新的协议，实现了浏览器与服务器**全双工**通信。其本质是先通过HTTP/HTTPS协议进行握手后创建一个用于交换数据的TCP连接，服务端与客户端通过此TCP连接进行实时通信。
#### 2.HTTP的局限性
* HTTP是**半双工**协议，也就是说，在同一时刻数据只能单向流动，客户端向服务器发送请求(单向的)，然后服务器响应请求(单向的)。

```js
var wsUri = "ws://echo.websocket.org/";
function testWebSocket() {
    websocket = new WebSocket(wsUri); //参数为需要连接的服务器端的地址
    //Browser和WebSocketServer连接成功
    websocket.onopen = function (evt) {
        console.log("Connection open ...");
        websocket.send("Hello WebSockets!");//向服务器发送数据
    };
    //Browser接收到WebSocketServer发送过来的数据
    websocket.onmessage = function (evt) {
        console.log("Received Message: " + evt.data);
        websocket.close();//关闭WebSocket连接或停止正在进行的连接请求
    };
    //连接失败，发送接收数据失败或者处理数据出现错误
    websocket.onerror = function (evt) {
        console.log("websocket error: " + evt.data);
    };
    //Browser接收到WebSocketServer端发送的关闭连接请求
    websocket.onclose = function (evt) {
        console.log("Connection closed.");
    };
}  
```

<br>

### 参考：
https://segmentfault.com/a/1190000018496938
