---
layout:     post
title:      JavaScript之Web Workers
subtitle:   Web Workers解析
date:       2019-9-18
author:     梦尽头
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - JavaScript
    - Web Workers
---

## 前言

随着Web应用复杂度的与日俱增，浏览器越来越复杂的计算在所难免。长时间运行的JavaScript进程会导致浏览器冻结用户界面，让人感觉屏幕锁死了。

**Web Workers为 Web 内容在后台线程中运行脚本提供了一种简单的方法。**

Web Workers可以运行异步JavaScript代码，避免阻塞用户界面。在执行复杂计算和数据处理的时候，此API很有用，否则，这些任务轻则会占用很长时间，重则导致用户无法与界面交互。

浏览器实现Web Workers规范的方式有很多种，可以使用线程、后台进程或进程运行在其他处理器核心上。

目前支持Web Workers的浏览器有IE10+、FF3.5+、Safari4+、Opera10.6+、Chrome和iOS版的Safari。

一个worker是使用一个构造函数创建的一个对象(`Worker()`) 运行一个命名的JavaScript文件 - 这个文件包含将在工作线程中运行的代码;

 workers 运行在另一个全局上下文中,不同于当前的window. 
因此，使用 window快捷方式获取当前全局的范围 (而不是self) 在一个 Worker 内将返回错误。

Web Workers适合异步排序、图片转换、通讯内容的加解密等比较耗时的任务。

## Web Workers的分类

- 专用Worker，专门只为某个特定的页面服务，不能在页面间共享。`DedicatedWorkerGlobalScope `对象代表了worker的上下文，专用worker仅仅能被首次生成它的脚本使用。

- 共享Worker，上下文是`SharedWorkerGlobalScope`对象，共享worker可以同时被多个脚本使用。


## Web Workers的限制

- Web Worker所执行的JavaScript代码完全在另一个作用域中，与当前页面的代码不共享作用域

- 不能访问页面中的DOM节点，不能动态去影响页面的外观

- 不能使用window对象的默认方法和属性

- 可访问简化的`navigator`对象，包含`onLine appName appVersion userAgent和platform`属性

- 可访问只读的location

- 可以使用定时器`setTimeout` 和`setInterval`

- 可以使用XMLHttpRequest 进行网络请求

- 可以使用WebSockets，IndexedDB以及FireFox OS专用的Data Store API等数据存储机制

## 专用worker

### 检测是否支持worker

为了更好的错误处理控制以及向下兼容，需要进行兼容性检测

```javascript
if (window.Worker) {
  //...

}else {
  // 不兼容    
}
```

### 专用worker初始化创建

创建一个新的worker很简单。只需调用`Worker()`构造器，指定一个脚本的URI来执行worker线程。

```javascript
// main.js
// 在页面脚本中初始化worker
var myWorker = new Worker('worker.js');
```

### 专用worker消息的接收和发送

- **使用`postMessage()`方法给worker发送传递消息。**

```javascript
// main.js

var myWorker = new Worker('worker.js');

first.onchange = function() {
  myWorker.postMessage([first.value,second.value]);
  console.log('Message posted to worker');
}

second.onchange = function() {
  myWorker.postMessage([first.value,second.value]);
  console.log('Message posted to worker');
}
```

这段代码中变量first和second代表2个<input>元素；

它们当中任意一个的值发生改变时，`myWorker.postMessage([first.value,second.value])`会将这2个值组成数组发送给worker。

**消息内容可以是任何能够被序列化的值。**


- **使用`onmessage`事件处理函数代码作为响应接收消息**

```javascript
// worker.js

onmessage = function(e) {
  console.log('Message received from main script');
  var workerResult = 'Result: ' + (e.data[0] * e.data[1]);
  console.log('Posting message back to main script');
  postMessage(workerResult);
}
```

`onmessage`处理函数允许我们在任何时刻，一旦接收到消息就可以执行一些代码，代码中消息本身作为事件的data属性进行使用。
这里我们简单的对这2个数字作乘法处理并再次使用`postMessage()`方法，将结果回传给主线程。


```javascript
// main.js

myWorker.onmessage = function(e) {
  result.textContent = e.data;
  console.log('Message received from worker');
}
```

回到主线程，我们再次使用onmessage以响应worker回传的消息：


### 终止worker

- 主线程内终止worker，调用`terminate`方法，worker的代码会立即停止执行，也不会触发error和message事件

```javascript
// main.js
myWorker.terminate();
```

- worker线程中，调用自己的 `close`方法进行关闭

```javascript
// worker.js
close();
```

### 错误处理

当 worker 出现运行中错误时，它的 `onerror` 事件处理函数会被调用，error事件对象包含三个属性：

- `filename`：发生错误的脚本文件名。

- `lineno`：发生错误时所在脚本文件的行号。

- `message`：可读性良好的错误消息。

该事件不会冒泡并且可以被取消；为了防止触发默认动作，worker 可以调用错误事件的 preventDefault()方法。


```javascript
// worker.js

worker.onerror = function(event) {
  console.log("ERROR:" + event.filename + "(" + event.lineno + "):" + event.message);
}
```

### 引入脚本和库

Worker 线程能够访问一个全局函数importScripts()来引入脚本，该函数接受0个或者多个URI作为参数来引入资源。

如果脚本无法加载，将抛出 NETWORK_ERROR 异常。

```javascript
importScripts();                        /* 什么都不引入 */
importScripts('foo.js');                /* 只引入 "foo.js" */
importScripts('foo.js', 'bar.js');      /* 引入两个脚本 */
```

## 共享worker

这里，我们关注一下专用worker和共享worker之间的区别。

在这个示例中有2个HTML页面，每个页面所包含的javascript代码使用的是同一个worker。

### 共享worker初始化创建

```javascript
// main.js

var myWorker = new SharedWorker('worker.js');
```

一个非常大的区别在于，与一个共享worker通信必须通过端口对象——一个确切的打开的端口供脚本与worker通信。

在传递消息之前，端口连接必须被显式的打开，打开方式是使用onmessage事件处理函数或者start()方法。

在使用start()方法打开端口连接时，如果父级线程和worker线程需要双向通信，那么它们都需要调用start()方法。
```javascript
myWorker.port.start();  // 父级线程中的调用
port.start(); // worker线程中的调用, 假设port变量代表一个端口
```

### 共享worker消息的接收和发送

- **使用`postMessage()`方法给worker发送传递消息，但是postMessage() 方法必须被端口对象调用**

```javascript
// main.js

var myWorker = new SharedWorker('worker.js');

first.onchange = function() {
    myWorker.port.postMessage([first.value, second.value]);
    console.log('Message posted to worker');
  }

  second.onchange = function() {
    myWorker.port.postMessage([first.value, second.value]);
    console.log('Message posted to worker');
  }
```

- - **使用`onmessage`事件处理函数代码作为响应接收消息**

```javascript
// worker.js

onconnect = function(e) {
  var port = e.ports[0];

  port.onmessage = function(e) {
    var workerResult = 'Result: ' + (e.data[0] * e.data[1]);
    port.postMessage(workerResult);
  }
}
```

首先，当一个端口连接被创建时（例如：在父级线程中，设置onmessage事件处理函数，或者显式调用start()方法时），使用onconnect事件处理函数来执行代码。

使用事件的ports属性来获取端口并存储在变量中。

然后，为端口添加一个消息处理函数用来做运算并回传结果给主线程。在worker线程中设置此消息处理函数也会隐式的打开与主线程的端口连接，因此这里跟前文一样，对port.start()的调用也是不必要的。

回到父级线程，我们接收并处理消息。

```javascript
myWorker.port.onmessage = function(e) {
  result2.textContent = e.data;
  console.log('Message received from worker');
}
```








