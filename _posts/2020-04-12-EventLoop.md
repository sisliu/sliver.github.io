---
layout:     post
title:      "事件循环机制EventLoop"
subtitle:   " \"宏观任务与微观任务前期知识准备\""
date:       2020-04-12 22:43:24
author:     "ZH"
header-img: "img/home-bg-art.jpg"
catalog: true
tags:
    - EventLoop
---
## EventLoop的相关概念
1. 堆(Heep)
堆表示一大块非结构化的内存区域，对象，数据被存放在堆中
2. 栈(Stack)
栈在javaScript中又称执行栈，调用栈，是一种后进先出的数组结构    
JavaScript有一个主线程和调用栈(或执行栈call-stack)，主线各所有的任务都会被放到调用栈等待主线程执行。    
js调用栈采用的是后进先出的规则，当函数执行的时候，会被添加到栈的顶部，当执行栈执行完成后，就会从栈顶移出，直到栈内被清空    
举个例子:    

```
function foo(b) {
  var a = 10;
  return a + b + 11;
}

function bar(x) {
  var y = 3;
  return foo(x * y);
}

console.log(bar(7)); // 返回 42
```
当调用bar时，创建了第一个帧，帧中包含了bar的参数和局部变量。当bar调用foo时，第二个帧就被创建，并被压在第一个帧之上，帧中包含了foo的局部变量。当foo返回时，最上层的帧就被弹出栈(剩下bar函数的调用栈)。当bar返回的时候，栈就空了。    
这里的堆栈，是数据结构的堆栈，不是内存中的堆栈(内存中的堆栈，堆存放引用类型的数据，栈存放基本类型的数据)

队列(Queue)
队列即任务队列(Task Queue),是一种先进先出的一种数据结构。在队尾添加元素，从队尾添加元素，从对头移出元素    

## 同步任务和异步任务
JavaScript是单线程，也就意味着所有任务都必须要排队，前一个任务结束，才会执行后面一个任务。于是JavaScript任务分为两种:同步任务，异步任务    
<font color=black>同步任务</font>是调用立即得到结果的任务，同步任务在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务    
<font color=black>异步任务</font>是调用无法立即得到结果，需要额外的操作才能预期结果的任务，异步任务不进入主线程，而是进入"任务队列"(task queue)的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。    
JS引擎遇到异步任务（DOM事件监听、网络请求、setTimeout计时器等），会交给相应的线程单独去维护异步任务，等待某个时机（计时器结束、网络请求成功、用户点击DOM），然后由 事件触发线程 将异步对应的 回调函数 加入到消息队列中，消息队列中的回调函数等待被执行。     
<font color=black>具体来说，异步运行机制如下：</font>    
（1）所有同步任务都在主线程上执行，形成一个[执行栈]    
（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。    
（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。    
（4）主线程不断重复上面的第三步    
主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）    

![avatar](/img/evelop.jpg)    
```
console.log('script start')

setTimeout(() => {
  console.log('timer 1 over')
}, 1000)

setTimeout(() => {
  console.log('timer 2 over')
}, 0)

console.log('script end')

// script start
// script end
// timer 2 over
// timer 1 over
```
## 宏任务和微任务
同步和异步执行机制在es5的情况下够用了，但是es6会有一些问题    
promise同样是用来处理异步的:    
```
console.log('script start')

setTimeout(function() {
    console.log('timer over')
}, 0)

Promise.resolve().then(function() {
    console.log('promise1')
}).then(function() {
    console.log('promise2')
})

console.log('script end')

// script start
// script end
// promise1
// promise2
// timer over
```
在这里有个新概念:macro task(宏任务)和micro task(微任务)    
所有任务分为宏任务和微任务两种       
宏任务:* script全部代码、setTimeout、setInterval、setImmediate（浏览器暂时不支持，只有IE10支持，具体可见MDN）、I/O、UI Rendering     
微任务:* Process.nextTick（Node独有）、Promise、Object.observe(废弃)、MutationObserver(具体使用方式查看这里)     
在挂起任务时，JS 引擎会将所有任务按照类别分到这两个队列中，首先在宏任务的队列中取出第一个任务，执行完毕后取出 微任务 队列中的所有任务顺序执行；之后再取 宏任务，周而复始，直至两个队列的任务都取完。    
![avatar](/img/macro.webp)  
给个图加深理解
![avatar](/img/micro.webp)  