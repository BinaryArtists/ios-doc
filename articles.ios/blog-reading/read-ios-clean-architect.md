# 读 iOS clean architect

主要谈论参考中，第2，第4，帖子是很老了，但值得一看。

-------------------

## 知识点

1. 基本概念
  * 实体（Entities）
    > An entity represents a domain-specific concept

  * Implementation specific code
    > Modules that abstract persistence, networking, etc will fall in this category.

  * 用例（Interactors）
    > 管理多实体交互。（Managing the interaction between multiple Entities）
    > 保存应用状态，鉴于iOS应用不是面向服务的。（Keep the application state, given that iOS apps are not service oriented.）
    > 给业务逻辑定义严格的边界。（Define a strict boundary around the business logic.）
    > 处理其他特定细节实现，如调用持久化层，网络等。（Deal with other implementation specific details like calling the persistence layer, networking to third-party systems, etc.）
    > 用例的公共接口要在主线程加载。（The public API of an interactor is tied to the main thread. Requests are made from the main thread and responses are returned to the main thread.）

  * 传输机制（Delivery mechanism）
    > 边界通信

  * 边界（Boundaries）
    > 暴露的接口。（These boundaries represent some request to the interactor and some response from it.）

  * 服务（Service）
    > 服务一般与app同样生命周期

2. 将概念梳理一下

分层没啥说的，层内如何类设计的？

  * 显示层：UIViewController（做了瘦身，如：类似android中的list adapter）
  * 业务逻辑层：Service（统计、日志），api（登陆、下订单），Interactor（logic，包括api）
  * 数据访问层：Interactor（DAO），persistence（cora data 模型），Entity（实体类）

其中，对于业务逻辑层，建议看下这个：[细说业务逻辑](http://www.uml.org.cn/zjjs/201008021.asp)

## 结合当前项目

总的来说，它给了一个比较清晰的架构，但现在app真是重型武器，后续再继续讨论。

1. service：类的组织方式，没有使用service＋manager，而是采用的郭虹宇，ThinkFramework中的package，以及后来在samurai中变成了namespace，都行。
2. 在interactor：主要使用了ViewModel，明确职责：数据获取、数据处理（处理到 显示层 可以直接用）（包括网络数据和本地持久话数据）。。。同时还设立了datacenter，存放较为通用的数据，与ViewModel是同一级，但不做数据处理，用宏替换，做伪解耦（decoupling）
3. persistence：不使用coredata，使用realm，也就是Active Record吧。

## （最后，做下翻译吧）the clean architect

“我更喜欢用 干净架构 来描述”。下面开始吧：



-------------------

## 参考与推荐

  1. [7. 使用Clean Architecture模式开发Android应用的详细教程](http://www.infoq.com/cn/articles/clean-architecture-model-to-develop-android-application)
  2. [the clean architect](8. https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  3. [安卓干净架构](https://github.com/dmilicic/Android-Clean-Boilerplate)
  4. [oc干净架构](https://github.com/luisobo/clean-architecture)
  5. [swift 干净架构](https://github.com/koutalou/iOS-CleanArchitecture/tree/master/iOSCleanArchitectureTwitterSample/Domain/Model)
