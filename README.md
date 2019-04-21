# FE-Interview-questions
自己收集的一些前端面试题，部分来自网络，部分自己写的，不保证绝对正确，如有错误可以提出。


### [基础题](./基础题.md)

### [HTML](./HTML.md)

### [Css](./Css.md)

### [Css兼容](./Css兼容.md)

### [Js](./Js.md)

### [前端性能优化](./前端性能优化.md)

### [Jq实现原理](./Jq实现原理.md)

### [Vue实现原理](./Vue原理.md)

### [算法相关](./算法.md)

### [设计模式](./设计模式.md)

### [通信协议](./通信协议.md)

### [其他](./其他.md)

### 答案待收集的问题

- tcp如何保证可靠性
- 如何预防中间人攻击
- 如何应对流量劫持
- ES6的Set的实现
- webpack的plugins和loaders的实现原理
- webpack 热更新原理
- webpack如何优化编译速度
- vue和react的区别、优缺点
- 事件循环机制，以及在node和浏览器环境中它的区别
- 一些常见状态码，如：301 302 307 308 401 403
- vue的nextTick实现原理
- XSS

##### DNS解析会出错吗，以及原因
会，可能有以下原因：
1. 网络不可用。
2. 域名劫持。
3. 域名被禁止解析，这个需要联系域名注册商解决。
4. DNS缓存记录未生效，这个也需要联系域名商解决。

##### dns解析流程
https://blog.csdn.net/YZS_L_H/article/details/69751353

##### 三次握手和四次挥手
https://juejin.im/post/5cb93204f265da039955d770

##### 发布订阅模式和观察者模式的异同
https://juejin.im/post/5af05d406fb9a07a9e4d2799

##### Vue3使用Proxy解决了哪些问题
Vue1、2使用的都是Object.defineProperty，它有如下缺点：
1. 无法监听es6的Set、WeakSet、Map、WeakMap的变化；
2. 无法监听Class类型的数据；
3. 无法监听对象属性的新加或者删除；
4. 无法监听数组元素的增加和删除；

Proxy就是解决了Object.defineProperty的一系列缺点。

还有一点：

`Object.defineProperty监听数据是要递归遍历所有的属性的`，如果数据庞大，那么遍历的时间就会长很多。同样的，`如果监听数据过大，那么Object.defineProperty用于监听数据变化的内存就会很大`。

Vue3使用Proxy后，将会对Vue的性能带来巨大的提升！


##### CI/CD是什么，以及流程
名词解释：

<ruby>持续集成<rt>Continuous Integration</rt></ruby> 

持续集成（CI）可以帮助开发人员更加频繁地（有时甚至每天）将代码更改合并到共享分支或“主干”中。一旦开发人员对应用所做的更改被合并，系统就会通过自动构建应用并运行不同级别的自动化测试（通常是单元测试和集成测试）来验证这些更改，确保这些更改没有对应用造成破坏。这意味着测试内容涵盖了从类和函数到构成整个应用的不同模块。如果自动化测试发现新代码和现有代码之间存在冲突，CI 可以更加轻松地快速修复这些错误。

<ruby>持续交付<rt>Continuous Delivery</rt></ruby>

完成 CI 中构建及单元测试和集成测试的自动化流程后，持续交付可自动将已验证的代码发布到存储库。为了实现高效的持续交付流程，务必要确保 CI 已内置于开发管道。持续交付的目标是拥有一个可随时部署到生产环境的代码库。

<ruby>持续部署<rt>Continuous Deployment</rt></ruby>

作为持续交付——自动将生产就绪型构建版本发布到代码存储库——的延伸，持续部署可以自动将应用发布到生产环境。由于在生产之前的管道阶段没有手动门控，因此持续部署在很大程度上都得依赖精心设计的测试自动化。

`持续`，指的不是`一直运行`而是指`随时可运行`


##### 参考：
> 移动端兼容：https://blog.csdn.net/hardgirls/article/details/51722519 <br>
> js继承：https://www.cnblogs.com/humin/p/4556820.html <br>
> vue原理：https://www.jianshu.com/p/c2fa36835d77 <br>
> 算法：https://www.cnblogs.com/beli/p/6297741.html <br>
