# React 生命周期
## 前言
* 讲述React生命周期的帖子太多
* 你以为认得几个生命周期函数就能过了
* 其实这些函数背后有很多不易察觉的机制
* 实际工作中，生命周期函数能玩出花来
* 深入捣鼓它，很有必要
## 实例代码
### Component初始挂载
#### 模板
```js
import React, { Component } from 'react'
import ReactDOM from 'react-dom'

class Example extends Component {
  constructor() {
    super()
  }
  static getDerivedStateFromProps(props, prevState)
  render() return
  componentDidMount()
  componentWillUnmount()
}

export default Example
```
#### 具体作用
#### `constructor`
```js
constructor(props) {
  super(props)
  this.state = {
    default: '初始化某些默认值'
  }
}
```
* 容器组件不具有`state`，因此不需要执行构造函数
* UI组件具有自身的`state`，需要执行
* `constructor()` 与 `super()` 要一起出现，否则报错
* 构造函数内要干的事情，用直接赋值的方式，给`state`设置初始值、默认值
#### `getDerivedStateFromProps`
```js
static getDerivedStateFromProps(props, prevState) {
  //比较新传进来的props与旧的state
  //从而实现涉及差值的功能
  return {
    //新的state
  }
}
```
* 挂载与更新，都会调用此函数
* 假如`state`中的某些值与`props`有关联，应当在此放置`setState`
* ~~删掉了的旧版生命周期函数~~内无处可去的业务逻辑，统一搬到这来
* 它是一个`static`函数，因此不能访问 ~~this~~ ，杜绝了开发者非纯操作
* Facebook用纯函数来约束`render`前的非纯操作，所以Ajax也不要放这里面
#### `render`
```js
render() {
  return <div />
}
```
* 必须`return something`
* 不能写入外部变量，即：函数要纯
#### `componentDidMount`
```js
componentDidMount() {
  //ajax请求
  this.setState({
    default: '响应结果的数据'
  })
  //添加订阅
  setInterval(()=>{})
  //获取DOM节点大小、位置
  let ele = ReactDOM.findDOMNode(this.refs.ele)
}
```
* 在此处`setState`会触发`re-render`
* 但`render`与`re-render`处于`浏览器屏幕更新`之前，所以变化对用户不可感知
#### `componentWillUnmount`
```js
componentWillUnmount() {
  //清除timer
  clearTimeout()
  clearInterval()
  //确保异步setState已结束
  this.setState({})
  //清除React生态之外的引用库，对DOM的管理
}
```
* 不应调用`setState`，因为该实例永久卸载，不会再渲染


### Component更新
#### 模板
```js
class Example extends Component {
  static getDerivedStateFromProps()
  shouldComponentUpdate()
  render()
  getSnapshotBeforeUpdate()
  componentDidUpdate()
}
```
#### 具体作用
#### `shouldComponentUpdate`
```js
shouldComponentUpdate(newProps, newState) {
  if('比较新旧props或state') {
    //来决定是否取消渲染
    return false
  }
  //要返回true默认自动渲染
  return true
}
```
#### `getSnapshotBeforeUpdate`
```js
getSnapshotBeforeUpdate(prevProps, prevState) {
  if('旧数据与新数据比较') {
    return '给componentDidUpdate传一个参数as snapshot'
  }
  return null
}
```
#### `componentDidUpdate`
```js
componentDidUpdate(prevProps, prevState, snapshot) {
  if(snapshot) {
    //修改其他数值来修改UI
  }
}
```
## 本文参考
* [React.Component](https://react.docschina.org/docs/react-component.html) - React官方文档
* [React v16.3之后的组件生命周期函数](https://zhuanlan.zhihu.com/p/38030418) - 程墨Morgan，`深入浅出React和Redux`作者
* [重新学习 React (一) 生命周期，Fiber 调度和更新机制](https://juejin.im/post/5cf34ef66fb9a07ee5660735) - saiyan，掘金网友
## 实验代码
```js
import React, { Component } from 'react'
import ReactDOM from 'react-dom'

class Test extends Component {
  constructor() {
    super()
    this.state = {
      number: 1
    }
  }

  add() {
    this.setState({
      number: this.state.number + 1
    })
  }

  render() {
    return (
      <div>
        <p>{this.state.number}</p>
        <button onClick={this.add.bind(this)}>+++</button>
        <Son number={this.state.number} />
      </div>
    )
  }
}

class Son extends Component {
  constructor() {
    super()
    this.state = {
      number: 0
    }
  }

  static getDerivedStateFromProps(props, prevState) {
    console.log(prevState.number)
    console.log(props.number)
    if (props.number % 2 === 0) {
      return {number: props.number * 2}
    } else {
      return {number: props.number * 3}
    }
  }

  shouldComponentUpdate(newP, newS) {
    if(newP.number == 6) {
      return false
    }
    return true
  }

  getSnapshotBeforeUpdate(prevProps) {
    if(prevProps.number == 8) {
      return '传过去'
    }
    return null
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    if(snapshot) {
      console.log(snapshot + '啦')
    }
  }

  render() {
    console.log(this.state.number)
    return (
      <div>
        <p>{this.state.number}</p>
      </div>
    )
  }
}

export default Test

class Example extends Component {
  constructor(props) {
    super(props)
    this.state = {
      default: '初始化某些默认值'
    }
  }

  static getDerivedStateFromProps(props, prevState) {
    //比较新传进来的props与旧的state
    //从而实现涉及差值的功能
  }

  render() {
    return <div />
  }

  componentDidMount() {
    //ajax请求
    this.setState({
      default: '响应结果的数据'
    })
    //添加订阅
    setInterval(()=>{})
    //获取DOM节点大小、位置
    let ele = ReactDOM.findDOMNode(this.refs.ele)
  }

  componentWillUnmount() {
    //清除timer
    clearTimeout()
    clearInterval()
    //确保异步setState已结束
    this.setState({})
    //清除React生态之外的引用库，对DOM的管理
  }
}
```