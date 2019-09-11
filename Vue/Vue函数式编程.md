# Vue 函数式编程
## 前言
* React是 `函数式编程` 的倡导者，甚至在api中加许多“条条框框”来限制开发者不纯的操作，试图改变用户的习惯，就像苹果一样
* Vue并没有苛求开发者用函数式（虽然Vue 3.0有点往这靠拢了），甚至还定义出很多语法糖，使得Vue平易近人，占据中国互联网中小企业
* 由于先熟悉的 React ，再接触 Vue ，所以想用 React 的思维来看 Vue ，尝试加入一些函数式的开发模式
* 但争论 `FP` 与 `OOP` 孰好孰坏是没有意义的，极端追求 `FP` 那也太抽象了，吃力不讨好。根据实际情况将两种模式混杂，才是最好
* 争论 React 和 Vue 更没有意义，写的东西好不好取决于电脑前面那个人，而不是他用的工具

## 函数式编程基本理念
* 函数是一等公民（意思是，函数可以作为传参，可以作为返回）
```js
//传入
Array.map((item) => {item * 2})
//传出
function closure() {
    var private = '私有变量'
    return function() {
        return private
    }
}
```
* 不可变量（只能 `const`，不能 `let`、`var`）
```js
//React无状态组件
function StatelessComponent(props) {
    const { content } = props
    return <div>{content}</div>
}
```
* 纯函数（修改`函数作用域`外部的变量，称为`副作用`）
```js
var user = [{
    id: '001',
    name: '沧浪侠客'
}]
//不纯
function addUser() {
    user.push({
        id: '002',
        name: '我自横刀向天笑'
    })
}
//纯
function addUser(user) {
    return {
        ...user,
        {
            id: '002',
            name: '我自横刀向天笑'
        }
    }
}
user = addUser(user)
```
副作用会增加认知负荷（Cognitive Load），读代码的时候要上下文翻来翻去，看这个函数到底作了什么操作，动了哪个变量。如果变量少、变量声明靠得近还好，最麻烦的是有些全局变量有隐式属性，属性的命名不在这个代码文件里，但代码运行时它又实实在在地在那里，只能去翻依赖，万一IDE还不能点路径直接打开文件....小叮当要打人了
## Vue分离`表现层`与`行为层`的一种方法
### 基本型：
```JS
//行为层
//Action Component
export default {
    render() {
        return this.$scopedSlots.default({})
    }
}
```
模板直接返回 `slot` 上的 `default`

```js
//视图层
<template>
    <action-component>
        <div slot-scope="{}">
            <span>此处已经是纯渲染的视图层了</span>
        </div>
    </action-component>
</template>
<script>
    export default {}
</script>
```
视图组件包裹方法组件

`slot-scope` 可以暴露 `Action层` 的业务数据和方法 给 `View层` 
### 进阶型
```js
//Action
props: {
    items: Array
},
methods: {
    addItem() {
        //...
    }
}
this.$scopedSlots.default({
    items: this.items,
    addItem: this.addItem
})
//View
<action-component :items="items">
    <div slot-scope="{ items, addItem }">
        <div v-for="item in items"></div>
    </div>
</action-component>
data() {
    return {
        items:[]
    }
}
```
总的来说，`视图层` 向 `行为层` 传入业务数据，`行为层` 通过scope传出去action供 `视图层` 使用

这么做的重要意义在于，前端能剥离出复用率特别高的行为模式。

比如，一个后台页面项目的开发，列表的展示、列表的增、列表的删...这些逻辑已经写过无数遍了，唯一经常改变的是公司设计同事提供的样式原型图。

假如我们提取出通用的行为模式，每次有新需求，我们前端都只需要关注slot里面的视图细节，业务方法只需调用Action Component里的