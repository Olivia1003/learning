# Node定时器
> 事件循环是 Node.js 处理非阻塞 I/O 操作的机制

### Node提供4个定时器
语言标准：
* setTimeout() 
* setInterval()

Node特有：
* setImmediate()
* process.nextTick()

### 事件循环之前
* 同步任务
* 发出异步请求
* 规划定时器生效的时间
* 执行process.nextTick()等等

**Node 执行完所有同步任务，接下来就会执行process.nextTick的任务队列。**  
**根据语言规格，Promise对象的回调函数，会进入异步任务里面的"微任务"（microtask）队列。微任务队列追加在process.nextTick队列的后面。**

因此，顺序为：
1. 同步任务
2. process.nextTick()
3. 微任务
```js
// 本轮循环执行（即同步任务一旦执行完成，就开始执行它们）
process.nextTick(() => console.log(3)); // Node特有
Promise.resolve().then(() => console.log(4));

// 次轮循环执行
setTimeout(() => console.log(1));
setImmediate(() => console.log(2)); // Node特有
```

### 事件循环（event loop）
![life circle](/images/JSEventLoop.png)

* 每一轮的事件循环，分成六个阶段。每个阶段都有一个 FIFO 队列来执行回调。
* 只有一个阶段的回调函数队列清空了，该执行的回调函数都执行了，事件循环才会进入下一个阶段。



#### 各阶段
1. timers：处理setTimeout()和setInterval()的回调函数（检查一下当前时间，是否满足定时器的条件）
2. I/O callbacks：回调函数（除了以下操作的回调函数）
    * setTimeout()和setInterval()的回调函数
    * setImmediate()的回调函数
    * 用于关闭请求的回调函数，比如socket.on('close', ...)
3. idle, prepare：该阶段只供 libuv 内部调用
4. poll：这个阶段是轮询时间，用于等待还未返回的 I/O 事件
5. check：setImmediate() 回调函数在这里执行
6. close callbacks：一些准备关闭的回调函数，如：socket.on('close', ...) 

### 参考
http://www.ruanyifeng.com/blog/2018/02/node-event-loop.html
https://nodejs.org/zh-cn/docs/guides/event-loop-timers-and-nexttick/
