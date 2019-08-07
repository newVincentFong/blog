# React 虚拟DOM树 与 Diff算法
## 前言
* `Virtual DOM` 相当于 `浏览器DOM` 的平行宇宙
* `Diff`算法不能加快 `DOM` 的操作，但能减少 `DOM` 的操作次数
## Virtual DOM
* `浏览器DOM` 节点的三要素
```html
<类型 data-property="属性">
  <子元素 />
</类型>
```
* 同理，`v-DOM` 也有三要素 `tag`、 `props`、 `children` 
* 实际等价代码：
```js
//v-dom
{
  tag: "div",
  props: {
    class: "container"
  },
  children: [
    "Hello World", 
    {
      tag: "ul",
      props: {},
      children: []
    }
  ]
}
```
```html
<!--浏览器DOM-->
<div class="container">
  Hello World
  <ul></ul>
</div>
```
* 总结：实际上 `浏览器DOM` 对象十分庞大，自身拥有很多属性例如 `clientWidth` 等等
* 因此用js来处理经过简化的 `v-DOM` ，十分快速
## Diff算法
### 三大策略
#### 按 `层级` diff

实际业务开发中，很少出现用户交互导致 `DOM树` 层级变化。

因此，此策略既符合实际场景需要，还降低算法复杂度， `O(n^3)` 到 `O(n)`

#### 按 `类型` diff

当新旧节点发生了 `类型` 的改变时，则并不进行子树的比较，直接创建新类型的 `VirtualDOM` ，替换旧节点

#### 列表的diff - `key`

同一 `层级` ，`类型` 相同，的元素形成一个列表

操作分为三种 `插入`、 `移动`、 `删除`

没 `key` 的列表，只能按顺序进行每个元素的对比；有 `key` 的列表，识别变化内容，直接修改
## 参考资料
* [探索Virtual DOM的前世今生](https://zhuanlan.zhihu.com/p/35876032) -百度外卖前端
* [如何理解虚拟DOM?](https://www.zhihu.com/question/29504639) -知乎
* [react 社区的误解](https://zhuanlan.zhihu.com/p/56749733) -知乎专栏