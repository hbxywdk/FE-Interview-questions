# FE-Interview-questions
自己收集的一些前端面试题，部分来自网络，部分自己写的，不保证绝对正确，如有错误可以提出。


### [基础题](./基础题.md)

### [HTML](./HTML.md)

### [Css](./Css.md)

### [Css兼容](./Css兼容.md)

### [Js](./Js.md)

### [Git](./Git/Git.md)

### [前端性能优化](./前端性能优化.md)

### [Jq实现原理](./Jq实现原理.md)

### [Vue](./Vue.md)

### [Vue实现原理](./Vue原理.md)

### [算法相关](./算法.md)

### [设计模式](./设计模式.md)

### [通信协议](./通信协议/通信协议.md)

### [安全](./安全.md)

### [其他](./其他.md)

### 答案待收集的问题

- ES6的Set的实现
- webpack的plugins和loaders的实现原理
- webpack 热更新原理
- webpack如何优化编译速度

#### WebP图片如何降级？
http://support.upyun.com/hc/kb/article/1031845/

##### 简述DNS解析流程
1. 首先在本地计算机的缓存中查找，找到则使用本地。没找到，则向DNS服务器发起查询请求。
2. 客户机将域名查询请求发送到本地DNS服务器，本地DNS服务器首先在该服务器管理的区域的记录中查找，如果找到该记录，则利用此记录进行解析；如果没有区域信息可以满足查询要求，服务器在本地的缓存中查找。
3. 如果本地服务器不能在本地找到客户机查询的信息，将客户机请求发送到根域名DNS服务器。
4. 根域名服务器负责解析客户机请求的根域部分，它将包含下一级域名信息的DNS服务器地址返回给客户机的DNS服务器地址。

tips：DNS通常使用UDP协议

https://blog.csdn.net/YZS_L_H/article/details/69751353


##### 发布订阅模式和观察者模式的异同
https://juejin.im/post/5af05d406fb9a07a9e4d2799



##### 参考：
> 移动端兼容：https://blog.csdn.net/hardgirls/article/details/51722519 <br>
> js继承：https://www.cnblogs.com/humin/p/4556820.html <br>
> vue原理：https://www.jianshu.com/p/c2fa36835d77 <br>
> 算法：https://www.cnblogs.com/beli/p/6297741.html <br>
