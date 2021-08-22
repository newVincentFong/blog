# Events

## Capturing and Bubbling

捕获 与 冒泡

> 以前 Netscape 网景浏览器用的是捕获机制，IE用的是冒泡机制

> 最终，现代浏览器，两个都采用。先走一遍捕获，后走一遍冒泡

## addEventListener()

`EventTarget` 对象所拥有的方法，诸如 `Element`、`Document`、`window`

```javascript
target.addEventListener('eventname', function() {}, {
    /** 事件回调默认都是冒泡阶段触发，set true就会改为捕获阶段触发 */
    capture: true,
    /** 只触发一次，触发完就自动销毁 */
    once: true,
    /** 默认是 false
     * window层级的touchstart、touchmove默认是true
     * true会不准内部call e.preventDefault()
     * 从而防止阻塞
     */
    passive: true,
})
```

## Event

* e.target

    触发这次事件的原始元素

* e.currentTarget

    捕获、冒泡途中，执行的listener的对应注册元素

### e.preventDefault

### e.stopPropagation

### 分类

* 0级事件

```html
<input onclick="handleClick()" />
```

```javascript
window.onload = function() {}
btn.onclick = function() {}
```

* 2级事件

addEventListener

* 3级事件

2级的基础上新增更多的事件类型