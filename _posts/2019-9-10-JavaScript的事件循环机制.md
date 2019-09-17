---
layout:     post
title:      JavaScript的事件循环机制
subtitle:   Event Loop解析
date:       2019-9-10
author:     梦尽头
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - JavaScript 
---
## 几个系统概念

- 进程（process）：是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位

- 线程（thread）：是进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位

- 进程和线程是操作系统的概念

- 一个程序至少有一个进程,一个进程至少有一个线程

- 进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率

## JavaScript是单线程的

JavaScript语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。

JavaScript的主要用途是与用户互动，以及操作DOM。

这决定了它只能是单线程，否则会带来很复杂的同步问题。

比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

## 事件循环的几个概念

![event_loop](https://lzweife.github.io/img/event_loop.svg)

- 栈（stack）：自动分配的内存空间，由系统自动释放；函数调用会形成一个栈帧（frame）。

- 堆（heap）：是动态分配的内存，大小不确定，也不会自动释放；对象被分配在一个堆中，即用以表示一大块非结构化的内存区域。

- 队列（queue）：一个 JavaScript 运行时包含了一个待处理的消息队列。每一个消息都关联着一个用以处理这个消息的函数。


举例：

```javascript
function bar() {
console.log('bar');
}

function foo() {
console.log('foo');
bar();
}

foo();
```

以上代码，先执行foo，再执行bar，执行过程中栈的变化如下：

![stack_change](https://lzweife.github.io/img/stack_change.png)

## 任务队列

单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。
如果前一个任务耗时很长，后一个任务就不得不一直等着。

JavaScript异步执行的运行机制如下

- 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。

- 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

- 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。
那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

- 主线程不断重复上面的第三步。


![queue](https://lzweife.github.io/img/queue.jpg)

任务队列中的事件，除了IO设备的事件以外，还包括一些用户产生的事件（比如点击、页面滚动等）。
只要指定过回调函数，这些事件发生时就会进入"任务队列"，等待主线程读取。

## callback回调函数

callback回调函数，就是那些会被主线程挂起来的代码。
异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

任务队列是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。

主线程的读取过程基本上是自动的，只要执行栈一清空，"任务队列"上第一位的事件就自动进入主线程。

但是，由于存在"定时器"功能，主线程首先要检查一下执行时间，某些事件只有到了规定的时间，才能返回主线程。

## Event Loop事件循环

![event_loop](https://lzweife.github.io/img/event_loop.png)

主线程从"任务队列"中读取事件，这个过程是循环不断的，这种运行机制称为Event Loop（事件循环）。

主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码调用各种外部API，
它们在"任务队列"中加入各种事件（click，load，done）。
只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数。

## setTimeout 定时器

除了放置异步任务的事件，"任务队列"还可以放置定时事件，即指定某些代码在多少时间之后执行。
这叫做"定时器"（timer）功能，也就是定时执行的代码。

- 函数 setTimeout 接受两个参数：待加入队列的消息和一个延迟（可选，默认为 0）。
这个延迟代表了消息被实际加入到队列的最小延迟时间。

- 如果队列中没有其它消息，在这段延迟时间过去之后，消息会被马上处理。

- 但是，如果有其它消息，setTimeout 消息必须等待其它消息处理完。
因此第二个参数仅仅表示最少延迟时间，而非确切的等待时间。


```javascript
const s = new Date().getSeconds();
    setTimeout(function() {
        // 输出 "2"，表示回调函数并没有在 500 毫秒之后立即执行
        console.log("Ran after " + (new Date().getSeconds() - s) + " seconds");
    }, 500);

    while(true) {
        if(new Date().getSeconds() - s >= 2) {
            console.log("Good, looped for 2 seconds");
            break;
        }
    }
    
// 输出
// Good, looped for 2 seconds
// Ran after 2 seconds
```

事件循环举例：

```javascript
setTimeout(function() {
        console.log('setTimeout');
    })
    new Promise(function(resolve) {
        console.log('promise');
        for (let i = 0; i < 10000; i++) {
            if(i === 10) {
                console.log('for');
            }
            i == 9999 && resolve('resolved');
        }
    }).then(function(val) {
        console.log(val);
    })
    console.log('console');
    
//输出
//promise
//for
//console
//resolved
//setTimeout
```

