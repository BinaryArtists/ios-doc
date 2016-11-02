## 讨论 iOS 应用架构

“维基百科上有个专门的页面，上面罗列了常见的形式逻辑错误和非形式逻辑错误。但实际上，导致人们犯下这些逻辑错误的最重要的原因只有两个：概念不清，拒绝接受不确定性。” —— 李笑来。

所以，我们要理清各种概念，而有关架构这档子事儿，概念很多。

### 思想：分层、隔离、解耦

1. 分层：UI层、逻辑层、数据层

2. 隔离

3. 解耦：
  > 解耦是代码解耦，也为复用提供可能，也是给人解耦。

### 模式：架构模式、设计模式


### 优秀框架的特征

  1. 重用
    > 为了避免重复劳动，降低成本。重用是框架实现中的最为核心的目标，提高复用度是框架的首要目的。
    > 层次分明，高度组件化，框架中的各个部件高度独立。
    > 部件细化，性能高效，内存占用率低。
    > 耦合度低（可拆可组）

  2. 高效
  3. 安全
    > 运行安全稳定，可以通过完善TDD测试机制来保障。

  4. 可扩展
  5. 简明
    > 一个复杂的架构，不论是测试，还是维护，都是困难的。

  6. 透明
    > 边界清晰，隐藏细节，暴露接口。
    > 提高了使用者效率，降低了学习的门槛。

### 框架设计的指导原则

  1. 概要框架总体功能目的 
  2. 对框架总体功能目的进行细化，分层（层次之间相对独立，互不干涉），仔细斟酌，理清层次之间的依赖关系（如，核心层，中间层，应用层）。    
    * 核心层：处于最低层，不依赖于框架中的其它层；
http://www.cnblogs.com/eugenewu0808/archive/2008/12/25/1362128.html    
    * 中间层：根据具体 框架 的需要自行定义，取名，注意理清层次之间的依赖关系；
http://www.cnblogs.com/eugenewu0808/archive/2008/12/25/1362123.html    
    * 应用层：在核心层以上，依赖于核心层；
http://www.cnblogs.com/eugenewu0808/archive/2008/12/25/1362122.html 
  3. 从核心层开始，逐层细化各个部件，仔细推敲部件名称，构思部件的功能，合理归类。

### 实施手段：模块化、组件化、服务化、插件化



### 流行的架构模式

  * MVC
    1.


  * MVVM
    1.
    2. 单页面交互模型, ![mvvm-single-view-model](https://github.com/BinaryArtists/not-just-code/blob/master/ios/imges/mvvm-single-view-model.png)
    3. ViewModel的责任（它其实就是个打杂的，可测试，但他的责任与“ViewModel”并不搭配。）
      * 用户输入数据验证
      * 网络数据获取
      * view数据处理（mapping a model value to a formatted string ）
      * VC的跳转控制逻辑（视图逻辑）
      * 数据绑定（RAC）
      * 数据变化通知（update）

  * MVP
    1.
    2. 单页面交互模型, ![mvp-single-view-model](https://github.com/BinaryArtists/not-just-code/blob/master/ios/imges/mvp-single-view-model.png)

  *


  * VIPER（View, Interactor, Presenter, Entity, Routing），并且，他的陈述者提到了[简明架构](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
    1.


* 然后，我们需要“理清概念”，写代码的时候，那些代码做了什么事，属于架构的哪一环，也就是要知道他的“责任”。
*

### 根据特性来选用架构

  * 特性：
    1. 可测试
    2. 可扩展

  * 分析
    1. (可测试、)：UI测试，业务测试 以及更细粒度的单元测试，层次划分、模块拆分很重要，可选用：MVVM，VIPER以及变种
    2.



### 原则

  * 1. （逻辑）拆分越细越好 2. 依赖关细越少越好 3. 交互越少越好 ... 相互矛盾时，如果没有特殊理由，以优先权高者胜出。
  * 2.


### 参考

  * 1. [iOS架构设计与分层](http://www.jianshu.com/p/58040a166a10)
    > 蔡学镛：我做（开发）架构的几个原则，根据优先次序高低排列：1. （逻辑）拆分越细越好 2. 依赖关细越少越好 3. 交互越少越好 ... 相互矛盾时，如果没有特殊理由，以优先权高者胜出。
    > ![vpbd-arch-1](https://github.com/BinaryArtists/not-just-code/blob/master/ios/imges/vpbd-arch-1.png)
    > 作者在实施时变成了：![vpbd-arch](https://github.com/BinaryArtists/not-just-code/blob/master/ios/imges/vpbd-arch.png)
    > ViewController(View)：管理View的层次结构、生命周期、一些组合过的View。
    > ViewModel：负责转换View需要的数据格式。
    > Presenter：显示View、ViewController的逻辑。
    > Router(Wireframe)：页面跳转逻辑。
    > Business：核心业务逻辑，复用性很高。
    > Model：基本数据模型，根据业务来定义。
    > DataSource：对于数据的抽象，对于Business层而言，不需要知道它是从网络、数据库还是缓存中得到的。
    > 这里将presenter和viewmodel分开，其实解决了viewModel责任过杂的嫌疑（以下有怀疑论者：滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答滴滴答答的等待*dddddddddddddddd


    *）*
    > 层次划分以及是否跨层依赖，基本决定团队规模和迭代速度。
    > 同层次，责任划分，绘出架构模型后，要看数据流产线有多长，架构对应着复杂度，团队规模和平均能力决定其复杂度的承受力。
    > 上面是结合外界因素分析短板，然后就要考虑架构合理性，以及它需要满足的基本特性，请看 chapter【原则】

  * 2. [简明架构](http://blog.8thlight.com/uncle-bob/2011/11/22/Clean-Architecture.html)
  * 3. [更轻量的 View Controllers](http://objccn.io/issue-1-1/)
  * 4. [测试 View Controllers](https://objccn.io/issue-13-5/)
  * 5. [使用 VIPER 构建 iOS 应用](https://objccn.io/issue-13-5/)
    > [VIPER TODO, 文章示例](https://github.com/objcio/issue-13-viper)
    > [VIPER SWIFT, 基于 Swift 的文章示例](https://github.com/objcio/issue-13-viper-swift)

  * 6. [Hello ZK MVVM](https://www.zkoss.org/wiki/Small_Talks/2011/October/Hello_ZK_MVVM)
  * 7. [Krush iOS Architecture](http://www.teehanlax.com/blog/krush-ios-architecture/)
    > 案例学习1: 网络层（服务器接口，CDN加速，请求队列，OAuth，请求重连机制，回调block通信机制，）
    > 案例学习2: 磁盘缓存
    > 案例学习3: 用户资料视图
    > 案例学习4: 以MVVM为主

  * 8. [Designing Rich Client Applications](https://msdn.microsoft.com/en-us/library/ee658087.aspx), [
Chapter 24: Designing Mobile Applications](https://msdn.microsoft.com/en-us/library/ee658087.aspx)
    > Figure 1 shows an overall view of typical rich client architecture, and identifies the components usually found in each layer.
    ![image](https://github.com/BinaryArtists/not-just-code/blob/master/ios/imges/IC350992.png)
    > A typical rich client application is decomposed into three layers: the presentation layer, business layer and data layer.
    >> the presentation layer:  UI and presentation logic components
    >> the business layer: business logic, business workflow and business entity components
    >> the data layer: data access and service agent components

  * 9. []()
