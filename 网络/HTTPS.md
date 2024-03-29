# HTTPS

## 一句话概括过程

* 加密前，客户端第一次先发送一个 `http` 请求到服务端 `443` 端口

* 服务端返回 **公钥** 和 **证书**

* 客户端验证 `证书` 没问题，就生成一个 **密钥**，用 `公钥` 加密 `密钥`，返回给服务端 (同时还附带一段 `密钥` 加密过的内容)

* 服务器用 `私钥` 解密得出 `密钥` (并用 `密钥` 解密那段内容，然后做同样的事情给客户端，确保协议是一致的)

* 之后服务端和客户端就能凭借这个密钥，作加密传输

本质就是，用非对称加密(`rsa`)，来护送对称加密的启动，靠对称加密保护传输安全

以上也叫 **SSL握手**

## 优点

* 黑客截取了 `密钥` 也没有用，没有 `私钥` 解密出来
* 用对称加密传输，而非全程非对称加密，减少性能消耗

![SSL Handshake](https://media.prod.mdn.mozit.cloud/attachments/2019/06/17/16746/d7ca8e7376987926d47aadb940f4cace/ssl.jpg)