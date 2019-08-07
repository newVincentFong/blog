# ES6之Proxy对象
## 前言
* 在业务开发中，对响应结果提取数据是书写频率很高的代码
```
//获取第一个用户的微信头像
res.data.user[0].wx_avatar
```
* 假如后端返回的 `user[]` 为空，这句代码会让整个程序就停下来
* 报错 `Uncaught TypeError: Cannot read property 'wx_avatar' of undefined`
* 过去常用的规避方法是用lodash库的get方法来取值
```
const _ = require('lodash')
let wx_avatar = _.get(res, 'data.user[0].wx_avatar', 1)
```
* 最近看到一个最优雅解决办法，用ES6新特性，Proxy对象
* 第一次看到的地方：[如何优雅地链式取值 -忽如寄](https://www.jianshu.com/p/e1c3d1015e70)
* 真正出自的地方：[无报错链式取值的几种方法 -百度外卖前端](https://zhuanlan.zhihu.com/p/29296692?group_id=891411987597230080)
* 所以大厂前端是真的流啤
## 实例代码
```
//精简版
function pointer(obj, path = []) {
  return new Proxy(()=>{}, {
    get (target, propKey) {
      return pointer(obj, path.concat(propKey))
    },
    apply (target, self, args) {
      let val = obj;
      let parent;
      for(let i = 0; i < path.length; i++) {
        if(val === null || val === undefined) break;
          parent = val;
          val = val[path[i]]    
        }
        if(val === null || val === undefined) {
          val = args[0]
        }
      return val;
    }
  })
}
```
## ES6文档
### Proxy( )
```
const proxy = new Proxy(target, handler)
```
* `target` 要代理的目标，是一个Object类型
* `handler` 代理配置，
### get( )
* `get(target, propKey, receiver)` 拦截Object属性的读取，即拦截 `res.data` 或 `res['data']`
* `target` 被代理的目标Object，即Proxy的第一个参数，此处为()=>{}
* `propKey` 属性键
* `receiver` Proxy实例自身
### apply( )
* `apply(target, ctx, args)` 拦截Proxy实例作为函数的调用
* 即拦截 `proxy(...args)` 或 `proxy.apply(this, args)` 或 `proxy.call(this, ...args)`
* `ctx` 上下文对象，即this
* `args` 被调用时传入的参数，放进了这个数组