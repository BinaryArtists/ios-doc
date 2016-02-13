## iOS 技能点

以下摘录几个，看客且当互补，同时一定要注意，技术革新厉害，要记得补充新鲜的营养液。...^_^_^...

### 几个主题（作为补充）

0. iOS 博客收集
  * [巧叔精心整理了国内40多位iOS开发博主的博客地址](https://github.com/tangqiaoboy/iOSBlogCN)
  * [objc.io](http://www.objc.io/)
  * [Ray Wenderlich](http://www.raywenderlich.com)
  * [iOS Developer Tips](http://iosdevelopertips.com/)
  * [iOS Dev Weekly](http://iosdevweekly.com/)
  * [NSHipster](http://nshipster.com/)
  * [Bartosz Ciechanowski](http://ciechanowski.me)
  * [Big Nerd Ranch Blog](http://blog.bignerdranch.com)
  * [Nils Hayat](http://nilsou.com/)
  * 巧叔又说：
    > 使用博客RSS聚合工具（例如Feedly：http://www.feedly.com/）可以获得更好的博客阅读体验。手机上也有很多优秀的博客阅读工具（我使用的是Newsify）。合理地使用这些工具也可以将你在地铁上、睡觉前等碎片时间充分利用上。
    > 去年出版的《iOS 7 Programming Pushing the Limits》 以及  《Objective-C高级编程：iOS与OS X多线程和内存管理》 都算是不错的进阶方面的读物
  * 

1. Xcode
  * 使用技巧
  * 调试技巧（lldb，chies？）

2. 网络
  * HTTP
    > [HTTP 2.0 那些事儿](http://mrpeak.cn/blog/http2/)
    > ？？？

### [巧叔的iOS 技能图谱](http://www.aiweibang.com/yuedu/tech/85762720.html)

1. 编程语言
  * swift
  * Objective-C
  * C++/C
  * JavaScript

2. 操作系统
  * Mac OSX
  * iOS
  * watchOS
  * tvOS
  * Linux 常用命令

3. 开发基础
  * UI 控件
  * Storyboard & Xib
  * Core Data & Sqlite（Database）
  * Core Graphics
  * Animation
  * Block & GCD
  * Test Case 编写
  * Framework
  * Autolayout
  * 手势识别、重力感应

4. 开发进阶
  * 引用计数
  * Runtime
  * Runloop
  * 对象模型
  * Hybrid....
  * 沙盒机制
  * AVFoundation
  * Core Text
  * 逆向与安全
    * class dump
    * IDA Pro
    * Hopper
    * iOS Class Guard

5. 设计模式
  * MVC(架构模式)
  * MVVM(架构模式)
  * notification
  * delegate
  * kvo
  * factory pattern
  * command pattern

6. 函数式编程（有人说，这应该是响应式？别开玩笑了。）
  * ReactiveCocoa
  * RxSwift

7. 开发工具
  * IDE
    > Xcode
    > AppCode
  * 调试工具
    > Charles, Wireshark
    > Reveal
    > Instruments
  * 插件
    > Alcatraz
    > VVDocument
    > XVim
    > FuzzyAutocompletePlugin
    > KSImageNamed-Xcode
  * 辅助工具
    > xScope
    > ImageOptim
    > 马克鳗
    > Dash
    > Deploymate
    > FauxPas
    > PaintCode
  * 命令行工具
    > xcodebuild, xcode-select
    > nomad
    > xctool
    > fastlane
  * 持续集成
    > Jenkins
    > Travis CI
    > Bot

8. 开源项目
  * AFNetworking & Alamofire
  * Masonry
  * SDWebImage
  * SwiftyJson
  * JSPatch
  * React Native

9. 包管理
  * CocoaPods
  * Carthage
  * Swift Package Manager

10. App 打包上传与审核
  * Apple Developer 网站
  * iTunes Connect 网站
  * IAP
  * 加急审核申请
  * 打包脚本

11. 第三方服务
  * 崩溃收集
    > Bugly
    > Crashlytics(Fabric)
    > BugHD
    > bugtags.com
  * 数据统计
    > Google Analytics
    > 友盟
    > MTA
    > Flurry
    > App Annie
  * 应用分发
    > TestFlight
    > 蒲公英
    > FIR
  * 集成平台
    > buddybuild.com

### [iOS 开发技能点集合](http://www.v2ex.com/t/169291)
略

### [iOS工程师技能列表](https://segmentfault.com/a/1190000002946644)

  * Objective-C
   > objective-c语言基础
   > [library,framework的制作](http://segmentfault.com/q/1010000002952316)
   > [Runtime编程](http://segmentfault.com/a/1190000002966213)
   > LLVM原理和调优

  * 操作系统
   > iOS内存管理和调优
   > iOS的文件系统和沙盒机制
   > iOS多线程编程（Thread,GCD,NSOperation）
   > iOS网络和服务器编程（NSURLConnection,NSURLSession）
   > iOS系统的各种安全机制

  * 网络编程
   > iOS网络发送机制调整和优化（NSURLSession）
   > Socket编程
   > 网络传输中的各种保障
   > 对传输协议的调整优化

  * 数据库&持久化方案
   > 常规持久化方案（Keychain,NSUserDefaults,Sqlite,CoreData）
   > 数据库的使用和设计（Sqlite）
   > 数据结构优化，Sql调优

  * 图形图像编程
   > UIKit,Core Animation和Core Text的绘制
   > Core Graphics, Quartz 2D, Media Player, AV Foundation
   > OpenGL ES, GLKit, SpriteKit, SceneKit, Metal

  * 数据结构 & 算法
   > 基本的算法和数据结构（排序搜索算法, 数组, 队列）
   > 较复杂数据结构的灵活应用（二叉树, 图等）
   > 复杂的专项算法（图像识别算法, 拓扑定位等等）

  * 安全方案
   > 本地数据存储安全（Keychain）
   > 授权和身份验证
   > 传输安全（对称, 非对称, SSL）
   > App代码安全

  * 业务能力
   > 一般性业务功能需求分析及实现
   > 重要业务模块的需求分析及实现
   > 中小规模产品的架构，系统设计和实现
   > 大规模产品或产品线的架构，系统设计和实现
   > 平台级产品的架构，系统设计和实现

  * 总结
   > 后续的文章中，我会按照这个技能列表的顺序，逐步完善这些细节。关于iOS开发的细节，上述只是自己总结和参考别人的建议所列，如果有不足，请在回复中指出。

### [iOS技能树](http://blog.csdn.net/doubleuto/article/details/40106195)

  ![ios.tech-tree](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/ios.tech-tree.png)
