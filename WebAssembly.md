# WebAssembly

## 定义
> WebAssembly/Wasm 是基于堆栈虚拟机的二进制指令格式。Wasm为了一个可移植的目标而设计的，可用于编译C/C+/RUST等高级语言，使客户端和服务器应用程序能够在Web上部署

> WebAssembly 或者 wasm 是一个可移植、体积小、加载快并且兼容 Web 的全新格式

> 这为客户端app提供了一种在网络平台以接近本地速度的方式运行多种语言编写的代码的方式

![life circle](/images/wasm.png)


<br/>

## 由来
#### JavaScript这门语言本身的缺陷
* JavaScript没有静态变量类型
* C / C++ 是静态类型语言，而 JS 是动态类型语言

#### asm.js出现
* 作为WebAssembly的前身，asm.js是一个Javascript的严格子集，asm.js是一个编译目标
* 它的变量一律都是静态类型，并且取消垃圾回收机制
* asm.js不能解决所有的问题

#### WebAssembly横空出世
* 在2015年，我们迎来了WebAssembly
* asm.js和WebAssembly的功能基本一致，就是转出来的代码不一样：asm.js 是文本，WebAssembly 是二进制字节码

## 特点
* 主流浏览器都已经支持 WebAssembly
* WebAssembly 字节码不能直接在任何一种 CPU 架构上运行， 但由于非常接近机器码，可以非常快的被翻译为对应架构的机器码，因此 WebAssembly 运行速度和机器码接近
* WebAssembly 作为一种底层字节码，除了能在浏览器中运行外，还能在其它环境运行

## 使用场景
* 对性能有很高要求的App/Module/游戏
* 在Web中使用C/C++/Rust/Go的库

### 参考
https://segmentfault.com/a/1190000016949129
