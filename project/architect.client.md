## 客户端架构类知识和可分享模块图谱

### 目录
* [1. iOS](#1)
  * [1.1 swift](#1.1)
  * [1.2 objective-C](#1.2)
    * [1.2.boss 常见模块与推荐解决方案](#1.2.boss)
  * [1.3 ReactNative](#1.3)
* [2. Android](#2)
  * [2.1 native](#2.1)
    * [2.1.boss 常见的模块与推荐的解决方案](#2.1.boss)
  * [2.2 ReactNative](#2.2)



<h3 id="1.2.boss">1.2 常见模块与推荐解决方案</h3>

注意：其中有些开源模块，仍然在制作中。
再次：可服用模块呢，其实和系统框架层次划分，很类似的。

1. 先说说框架层
  * 如果将将一个工程的代码，笼统氛围可复用、不可复用（易变）两部分，则可以粗浅的认为可复用，可以单独抽出来作为“框架”
  * “如果你想较实在的评价一部电影 ，你首先需做到的是对它进行分类”，如果要编写框架代码，则要在垂直方向上下功夫
  * 再回顾一下iOS系统框架分层：CocoaTouch（UIkit）,Media（CoreAnimation）,CoreServices（coreData、Foundation、CFNetwork）,CoreOS
  * 那我们编写可复用组件的时候，结构方面可以参考BeeFramework（它写的很棒，它不光提供直接可复用组件，也实现了“插件式”，还有部分编码约定）：
    - [Features]
    - CLI
    - MVC
      - View
        - Liveload
        - Application
        - Config
        - Container
          - Board
          - Stack
          - Router
          - Window
        - CSS style sheet
        - XML template
        - DOM
          - Animation/Transition
          - Data binding
          - Capability
          - Elements
          - Elements ext
          - Signaling
          - Auto layout
          - Query (jQuery-like syntax)
        - View-Model
          - Once
          - Paging
          - Stream
        - Other
          - Color
          - Font
          - Image
          - Metrics
      - Model
      - Controller
        - Message
        - MessageController
        - Queue
        - Routine
        - Extensions
          - Message + JSON
          - Message + HTTP
          - Message + XML
          - Message + ActiveRecord
    - System
      - Cache
        - File
        - Memory
        - Keychain
        - UserDefaults
      - Database
        - SQLite wrapper
        - ActiveRecord
        - Driver
      - Foundation
        - Assertion
        - Log
        - Performance
        - Runtime
        - Sandbox
        - Singleton
        - System information
        - Thread
        - Ticker
        - UnitTest
      - Localization
      - Network
        - HTTP client
        - HTTP server
        - Reachability
        - Socket
      - Resource
      - Service
  * 它们的使用方式，大致是：派生、组合、装饰（切面）等

2. 再备案一下我使用的一套方案（初衷在于，通用不臃肿）
  * vendor：内部或外部开源的，较为深度定制的、独立的子模块，如XXTextView
  * oc.tool：（作为框架的最底层）script、类别（基于foundation）、基类、显式的编码约定（这点可参照Bee中的@notification等）
  * Storage：对象的缓存方案
  * Network：为api请求、文件下载等提供的网络支持、实体的序列化和反序列化
  * Testor：（未来可在手机端直接使用的提测方案）、单元自动化测试等
  * support：（这一层有点意思）一些可以用的编程模型，如：状态机、生产消费者模型等

2.1 从应用场景角度
  * 图片缓存：
  * 即时聊天：
    > 环信
    > [因为公司需求要在安卓和 ios 客户端上做即时聊天系统，请问有什么好的解决方案](http://www.v2ex.com/t/125584)
    > [最近要用到IM，比较了下LeanCloud、融云、环信，请问LeanCloud有什么优势？](http://www.zhihu.com/question/26779860)
    > [LeanCloud 开放资源](https://open.leancloud.cn)
  * 二维码（[未整理！！！](http://www.v2ex.com/t/253312#reply14)）
   > CIDetector
   > photoKit
   > QRCode
   > 二维码的图像识别算法， GlobalHistogramBinarizer 和 HybridBinarizer 分别适用不同的场景，图片识别的话要看图片属于什么类型的，黑白的肯定是前者识别更有效，带有渐变的或者有阴影的则采用后一个算法。
   > [http://zxing.org/w/decode.jspx](http://zxing.org/w/decode.jspx)
  * ？？？？

<h3 id="2.1.boss">2.1 常见模块与推荐解决方案</h3>

@db
