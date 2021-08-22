# VueRouter

单页应用的路由，需要实现跳转时无刷新，也就是不触发后端请求

因此探索出两种模式

* Hash 模式
* History 模式

## Hash 模式

利用 url 中的锚点，也就是 `#` 号后面的部分，改变不触发请求

将 `/xxx/yyy` 路由信息放在 `#` 号之后

通过监听 `hashchange` 事件，来更新路由状态

```javascript
window.addEventListener('hashchange', function() {})
```

缺点

* url 不好看，不自然

## History 模式

利用 HTML5 的新特性 HistoryAPI

浏览器提供 api，能修改路由历史，而不触发请求

具体名称叫

* pushState()
* replaceState()

事件有

```javascript
window.addEventListener('popstate', function() {})
```

> 同时，需要后端配置配合，将所有路由映射到根路由

> 将路由的权力，交给前端

缺点

* 旧版本浏览器不具备这个能力

## 架构

都需要实现

* 路由映射 route-map

    在项目 runtime 初始化的时候加载好映射

* 匹配规则 matcher

    对 url 路径解析

    一些通配符 `*` 的匹配功能

    ......

抽象出路由的 api，封装出来，常见的有

* go()

    接收一个数字

    `go(-1)` 就是后退

* push()

    跳转

* replace()

    添加历史

以上 api 都会有对应的 Hash、History模式实现