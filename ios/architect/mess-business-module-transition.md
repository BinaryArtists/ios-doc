## 重业务模块的页面跳转问题

注意：是业务模块的跳转协议！！！！！！！不一定是所有页面的！！！！！！！！

也就是说，为了达到解耦的目的，组件之间调用杜绝代码侵入。

### 评估考察点

  1. 页面、模块耦合度
    * 界面解耦带来的协作弹性
  2. 调试顺畅度
  3. 装饰器模式的灵活性（比如装饰个下面的技术点），也就是扩展灵活性

### 技术点

  1. 跳转动画
  2. 内链、外链

### 方案一：route by URL

  * 代表作：
    1. [URLManager](https://github.com/gaosboy/urlmanager)
    2. [DarielChen/DCURLRouter](https://github.com/DarielChen/DCURLRouter)
    3. [oenius/DarwinNativeRouter](https://github.com/oenius/DarwinNativeRouter)

  * 优点：
    1. 页面跳转解耦
    2. 可通过配置实现重写规则动态化

  * 缺点
    1. 不容易梳理逻辑（可以使用宏生成协议，技巧性）
    2. URL称为一个内部及与外部握手的协议，容易变得异构化严重（异构化，url协议本身规则滥用）
    3. 容易产生不符合W3C的URL标准

  * 如何运作的？

    a. 协议格式约定
    b. 注册

### 方案二：route by dependency injection

  * 代表作：
    1.

  * 优点：
    1.

  * 缺点


### 总结

  1. 绝对的解耦，我认为解决的其实是“人”的问题，而非业务问题，减少沟通成本，提高响应速度，带来了学习成本、维护成本。
  2. 统一：普通界面弹出与跳转，web跳转，reactnative跳转，app跳转，界面深链

### 参考

  1. [解耦神器 —— 统跳协议和Rewrite引擎](http://pingguohe.net/2015/11/24/Navigator-and-Rewrite.html)
    * 约定协议：URL必须是符合W3C URL，入：sharekit.tm/doShare
    * 定义模型：Application 包含多个 Module， Module 包含多个服务，同时实现一个 Config（sharekit_bundle.plist），Config 将各服务注册到 router。
    * 约定通讯协议（传参）：统跳协议声明了一个服务接口，这个接口中只有一个方法，服务必须由该接口实现而来：TMShareUrlHandler
    * 跳转实现：
    * Rewrite： Rewrite引擎的思路来源于Web容器中的Rewrite机制，主要解决天猫App中URL平台展现一致性的问题。

  2. [如何优雅的实现界面跳转 之 原生界面跳转的统跳实践 - DarwinNativeDispatcher](http://www.jianshu.com/p/d37a8cef42fb)
  2.1 [如何优雅的实现界面跳转 之 统跳协议 - DarwinNativeRouter](http://www.jianshu.com/p/d6a246752ca5?appinstall=0)
    * 全局路由协议能解决的问题
      > 错中复杂的Controller的跳转依赖
      > 推送通知，点击打开指定页面
      > app间通讯 及 deeplink
      > 一致的行为处理， Hybrid & React Native
    ^_^ 非常具有参考意义
  3. [客户端动态化系列之——URLroute](http://www.tuicool.com/articles/M36ZZzv)
  4. [campusappcn/iOSUrlRoute](https://github.com/campusappcn/iOSUrlRoute)
  5. [GirlCunt/URLRouter](https://github.com/GirlCunt/URLRouter)
  6. [joeldev/JLRoutes](https://github.com/joeldev/JLRoutes)
