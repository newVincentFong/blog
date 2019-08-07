# 你所不知道的JS(上)
## AST与编译器
* LHS : `a = 2`
* RHS : `console.log(a)`
* 分别是赋值和取值
## 作用域嵌套与作用域链
### 规则
* 引擎从当前的执行作用域开始查找变量，如果找不到， 就向上一级继续查找
* 当抵达最外层的全局作用域时，无论找到还是没找到，查找过程都会停止
### 实现
* 作用域链本质是函数属性`[[scope]]`，是一个数组，也是一个栈
* 每进入一个嵌套，就会将此作用域及其变量，压栈到数组的开头
* 要查找变量时，就对`[[scope]]`遍历
```
[[Scopes]]: Scopes[3]
0: Closure {J: ƒ, e: ƒ, f: undefined, g: undefined, h: init(1), …}
1: Closure {b: undefined, a: Window, cy: ƒ, cu: ƒ, ct: ƒ, …}
2: Global {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
```
## 变量提升
* `var a = 2` 被分成 `var a` 和 `a = 2`
* 经典例子：
```
foo() //TypeError
bar() //ReferenceError

var foo = function bar() {
  //...
}
```
```
var foo

foo() //TypeError
bar() //ReferenceError

foo = function() {
  var bar = ...self...
  //...
}
```