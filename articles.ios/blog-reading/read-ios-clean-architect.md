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

  ![The Clean Architecture](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/00004.jpg)

  1. 近几年系统架构的想法：
    * 六边形架构
    * [洋葱架构](http://jeffreypalermo.com/blog/the-onion-architecture-part-1/)

![传统分层架构](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/00005.png), ![洋葱架构](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/00006.png)

      > Hexagonal architecture and Onion Architecture share the following premise:  Externalize infrastructure and write adapter code so that the infrastructure does not become tightly coupled.

    * [高举架构](http://blog.8thlight.com/uncle-bob/2011/09/30/Screaming-Architecture.html)
      > Architectures are not (or should not) be about frameworks. Architectures should not be supplied by frameworks. Frameworks are tools to be used, not architectures to be conformed to. If your architecture is based on frameworks, then it cannot be based on your use cases.
      > 是说，架构不应该关乎框架。架构不应该由框架支撑，框架只是工具的使用。

  2. 摘录
    * They all have the same objective, which is the separation of concerns.They all achieve this separation by dividing the software into layers. Each has at least one layer for business rules, and another for interfaces.
      > 这些架构都很相似，它们有统一的目的，那就是关注点分离。通过分离，将软件分割层次。

    * 架构特征
      > (不依赖框架)Independent of Frameworks. The architecture does not depend on the existence of some library of feature laden software. This allows you to use such frameworks as tools, rather than having to cram your system into their limited constraints.
      > (可测试)Testable. The business rules can be tested without the UI, Database, Web Server, or any other external element.
      > (不依赖UI)Independent of UI. The UI can change easily, without changing the rest of the system. A Web UI could be replaced with a console UI, for example, without changing the business rules.
      > (不依赖数据库)Independent of Database. You can swap out Oracle or SQL Server, for Mongo, BigTable, CouchDB, or something else. Your business rules are not bound to the database.
      > (不依赖外部代理)Independent of any external agency. In fact your business rules simply don’t know anything at all about the outside world.

    * 依赖规则
      > Nothing in an inner circle can know anything at all about something in an outer circle.
      > By the same token, data formats used in an outer circle should not be used by an inner circle
      > [关注点分离]In particular, the name of something declared in an outer circle must not be mentioned by the code in the an inner circle. That includes, functions, classes. variables, or any other named software entity.

    * 实体
      > Entities encapsulate Enterprise wide business rules.
      > [关注点分离]They are the least likely to change when something external changes. For example, you would not expect these objects to be affected by a change to page navigation, or security. No operational change to any particular application should affect the entity layer.

    * 用例(Use Cases)
      > The software in this layer contains application specific business rules.
      > These use cases orchestrate the flow of data to and from the entities, and direct those entities to use their enterprise wide business rules to achieve the goals of the use case.
      > [关注点分离]We do not expect changes in this layer to affect the entities. We also do not expect this layer to be affected by changes to externalities such as the database, the UI, or any of the common frameworks. This layer is isolated from such concerns.

    * 接口适配
      > The software in this layer is a set of adapters that convert data from the format most convenient for the use cases and entities, to the format most convenient for some external agency such as the Database or the Web.

    * 框架和驱动
      > The outermost layer is generally composed of frameworks and tools such as the Database, the Web Framework

    * 只有四个环？
      > No, the circles are schematic. You may find that you need more than just these four.
      > As you move inwards the level of abstraction increases. The outermost circle is low level concrete detail. As you move inwards the software grows more abstract, and encapsulates higher level policies. The inner most circle is the most general.

    * 跨边界，什么数据跨边界？
      > DIP
      > Typically the data that crosses the boundaries is simple data structures.
      > basic structs, simple Data Transfer objects, arguments in function calls, pack it into a hashmap[ The important thing is that isolated]
      > [关注点分离]For example, many database frameworks return a convenient data format in response to a query. We might call this a RowStructure. We don’t want to pass that row structure inwards across a boundary. That would violate The Dependency Rule because it would force an inner circle to know something about an outer circle.
      > 在我看来啊，用数据对象来跨边界是没问题的，但是带有特定逻辑的数据对象（如RowStructure）呢，就过分啦。

-------------------

## 参考与推荐

  1. [7. 使用Clean Architecture模式开发Android应用的详细教程](http://www.infoq.com/cn/articles/clean-architecture-model-to-develop-android-application)
  2. [the clean architect](8. https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  3. [安卓干净架构](https://github.com/dmilicic/Android-Clean-Boilerplate)
  4. [oc干净架构](https://github.com/luisobo/clean-architecture)
  5. [swift 干净架构](https://github.com/koutalou/iOS-CleanArchitecture/tree/master/iOSCleanArchitectureTwitterSample/Domain/Model)
