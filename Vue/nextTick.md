# nextTick

## 官方文档

`Vue` 类和实例上都有这个方法

* [Vue.nextTick](https://cn.vuejs.org/v2/api/#Vue-nextTick)
* [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)

两者功能相同，只不过传入 `$nextTick` 的回调函数里能通过 `this` 访问到当前实例

目的是使一些修改、操作，能异步执行，能放在在下一次 `DOM` 更新后执行

因为有些操作可能依赖 `DOM` 上的一些数据，所以肯定得等到 `DOM` 被真实渲染了才执行

## Vue中的异步

`Vue` 更新 `DOM` 的操作是 [**异步**](https://cn.vuejs.org/v2/guide/reactivity.html#%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0%E9%98%9F%E5%88%97) 的

因为总不能一丁点的赋值操作，就响应到页面的 `DOM` 里，这样更新太频繁了、太不必要了

`Vue` 是会把赋值操作缓冲起来的

具体就是会开启一个队列，同一次事件循环中的数据变更 `watcher` 都会排进来

同一个 `watcher` 被触发多次，也只会排一个进队伍（根据 `watcher.id` 去重）

然后在下一个事件循环中把队列冲刷掉，逐个执行 `watcher.update` ，更新实际的 `DOM`

    储存数据变更的回调 - 储存的同时去重 - 冲刷队列 - 更新实际DOM

所以框架底层无时无刻充斥着 `nextTick` 的过程

而且队列冲刷前，会把队列 `watcher.id` 按 **升序** 去排列，因为 `id` 越大意味着 `watcher` 越年轻，意味着会是前面的子组件
。父组件先更新，就能确保之后子组件更新时，获取的是最新、最准确的 `props`
## 代码实现

`nextTick` 的实现方法经历过不少变动

一开始把回掉函数包装成 `微任务` ，后来又尝试了包成 `宏任务` ，不久又换回来 `微任务` 了

它会根据浏览器的兼容性，尝试使用以下原生api

* `Promise.then`
* `MutationObserver`
* `setImmediate` （宏任务）
* `setTimeout(fn, 0)` （宏任务）

前两个其实都是浏览器生成微任务的 `hack` 方法

新的原生api还有 `queueMicrotask` ，不过 `Vue` 还没采用而已