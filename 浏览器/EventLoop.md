# Event Loop

## 起因

* `Javascript` 是单线程的，因为它是服务于 `UI渲染` 的，采用单线程能降低开发的复杂性，确保得出的渲染结果总是一致
* 每个页面主要干活的是主线程，但任务繁多，一下子并发，就需要一个调度机制来提高效率，因此有了 `Event Loop`

## 给任务分类

* 基本计算
* UI 渲染
* 本地 I/O
* 网络 I/O
* ......

以上都是页面中常见的任务，可以看得出，有些任务耗时很短，有些耗时很久

因此耗时久的，就不能让它 **阻塞** 珍贵的主线程

得出一下分类：

阻塞 - 同步任务

    在主线程里排队，只有前一个任务执行完毕，才能执行后一个任务

    例如 alert()

非阻塞 - 异步任务

    不进入主线程，进入"任务队列"（task queue）

    "任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行

## 异步任务分类

宏任务（`macroTask`）

* setTimeout
* setInterval
* setImmediate
* UI rendering

浏览器会在每个宏任务之间渲染页面（`task` - 渲染 - `task` - ...）

微任务（`microTask`）

* `Promise.then`
* `process.nextTick` ( `node端` )
* `MutationObserver`
* `queneMicrotask`

执行栈空闲时，会清空一遍微任务队列，全部执行掉

假如期间产生了新的微任务，会排到队伍末尾，也会被执行到

然后再取一个宏任务进执行栈，执行完空闲后又回到开头那一步，如此往复

以上就是一次 `事件循环 EventLoop`

## node环境下的事件循环

与浏览器不同，node用 `libuv` 引擎实现事件循环

`process.nextTick`

把回调加到队列头

`setImmediate`

把回调加到队列尾

两者命名的意义刚好弄反了