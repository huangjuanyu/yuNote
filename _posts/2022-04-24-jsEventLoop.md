---
layout: post
title: js event loop 及 宏任务和微任务
category: root
summary: 'js event loop 及 宏任务和微任务的区别和用法'
author: hjy
---

## 1、js 运行机制

JS 执行是单线程的，它是基于事件循环的。事件循环大致分为以下几个步骤：

（1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。

（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

（4）主线程不断重复上面的第三步。

## 2、宏任务（macrotask ）和微任务（microtask ）

macrotask 和 microtask 表示异步任务的两种分类。

在挂起任务时，JS 引擎会将所有任务按照类别分到这两个队列中，首先在 macrotask 的队列（这个队列也被叫做 task queue）中取出第一个任务，执行完毕后取出 microtask 队列中的所有任务顺序执行；之后再取 macrotask 任务，周而复始，直至两个队列的任务都取完。


宏任务一般是：包括整体代码script，setTimeout，setInterval、I/O、UI render。
微任务主要是：Promise、Object.observe、MutationObserver。

* 宏任务和微任务之间的关系

![image]({{ "/assets/images/jsEventLoop1.png" | relative_url }})

### 具体体现demo

* 1.Promise在前，setTimeout在后
  
````javascript
new Promise((resolve) => {
    console.log('外层宏事件2');
    resolve()
}).then(() => {
    console.log('微事件1');
}).then(()=>{
    console.log('微事件2')
})
console.log('外层宏事件1');
setTimeout(() => {
    //执行后 回调一个宏事件
    console.log('内层宏事件3')
}, 0)
````
结果：
```javascript
外层宏事件2
外层宏事件1
微事件1
微事件2
内层宏事件3
```
* 2.setTimeout在前，Promise在后
  
````javascript
setTimeout(() => {
    //执行后 回调一个宏事件
    console.log('内层宏事件3')
}, 0)
console.log('外层宏事件1');

new Promise((resolve) => {
    console.log('外层宏事件2');
    resolve()
}).then(() => {
    console.log('微事件1');
}).then(()=>{
    console.log('微事件2')
})
````
结果：
````javascript
外层宏事件1
外层宏事件2
微事件1
微事件2
内层宏事件3
````
注意： ES6 规定，Promise对象是一个构造函数，用来生成Promise实例。 Promise 创建后会立即执行
## 3、总结---执行过程

1、先执行执行栈中的同步任务，然后所有微任务

2、从任务队列中取出一个宏任务放入执行栈，执行同步任务完毕，执行所有微任务

3、循环1、2步。
  
