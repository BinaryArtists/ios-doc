## iOS 进阶

工具、实践、理论（借喻：兵器、招式、内功）

### 一些博客

1. [作者整理的相关博客](https://github.com/tangqiaoboy/iOSBlogCN)
2. [objc.io](http://www.objc.io/)
3. [Ray Wenderlich](http://www.raywenderlich.com)
4. [iOS Developer Tips](http://iosdevelopertips.com)
5. [iOS Dev Weekly](http://iosdevweekly.com)
6. [NSHipster](http://nshipster.com/)
7. [Bartosz Ciechanowski](http://ciechanowski.me)
8. [Big Nerd Ranch Blog](http://blog.bignerdranch.com)
9. [Nils Hayat](http://nilsou.com)

使用博客RSS聚合工具，Feedly，可以获得很好的阅读体验。
作者使用的是：Newsify。

*推荐使用Newsify*

### 工具集

1. 包依赖工具：CocoaPods（java：Maven，Node.js：npm）
  > 查找第三方库，如：pod search json
  > 使用私有的pods，可以直接指定某一个依赖的podspec，这样就可以使用企业内部私有库：
  如：pod 'MyCommon', :podspec => 'https://yuaniku.com/common/myCommon.podspec'

2. 网络封包分析工具：Charles
  * 主要功能：
    > 支持SSL代理
    > 支持流量控制
    > 支持AJAX调试
    > 支持AMF调试
    > 支持重发网络请求
    > 支持修改网络请求参数
    > 支持网络请求的截获和动态修改
    > 检查HTML，CSS和RSS内容是否符合W3C标准

  * 主要使用：
    > 安装SSL证书
    > 将Charles设置为系统代理
    > 过滤网络请求

    > 截取iPhone上的网络封包

3. ［推荐］界面调试工具 Reveal（[分析iOS UI的利器Reveal安装破解教程.简书](http://www.jianshu.com/p/0cc7089143a3)

4. 移动统计工具 Flurry
  * 支持平台：iPhone, iPad, Android, Windows Phone, Java ME, BlackBerry
  * 统计内容：略

  * （我们用的Fabric，收购了Crashlytics。。。。。。国内友盟）

5. ［推荐］崩溃日志记录工具 Crashlytics

6. App Store 统计工具 App Annie
  * 统计App在App store的下载量、排名变化、销售收入情况及用户评价

### Xcode 插件

1. ［推荐］Xcode 插件管理工具：Alcatraz

2. KSImageNamed：[UIImage imageNamed:]键入，图片资源预览

3. XVim：可以在Xcode的编辑窗口开启vim模式

4. FuzzyAutocompletePlugin：允许模糊方式进行代码自动补全
*可忽略，Xcode7.3已经支持*

5. XTodo：查找所有的带有TODO、FIXME、???、!!!标记的注释

6. BBUDebuggerTuckAway：在你编辑代码的时候自动隐藏底部调试窗口，给编辑界面更多空间，方便我么修改代码。

7. SCXcodeSwitchExpander：帮助你迅速地在switch语句中填充枚举类型的每种可能的取值

8. Deriveddata-exterminator：清除Xcode缓存目录的插件

9. VVDocumenter：自动生成代码注释的工具，可以方便地把函数的参数名和返回值提取出来，这样结合appledoc命令，可以方便的将帮助文档输出。

10. ClangFormat：自动调整代码风格的工具

11. ColorSense：一个UIColor颜色输入辅助工具，可以帮助你在编写UIColor代码的时候，实时预览相应的颜色。（用处不大）

12. XcodeBoost：包含多个辅助修改代码的小功能
  * 方便将.m文件中方法定义，暴露到相应的.h文件中
  * 在某一个源文件直接输入正则表达式查找
  * 复制粘贴代码，不启用Xcode自动缩进功能

### 其他Xcode辅助工具

1. 取色器：数码测色计（DigitalColor Meter）

2. ImageOptim：免费的图像压缩工具（好于iOS工程默认使用的pngcrush命令）
  * 实现原理是使用各种开源的图像压缩工具，然后取效果最好的一个，包括：PNGOUT、Zopfli、PNGCrush、AdvPNG、extended OptiPNG、JpegOptim、jpegrescan、jpegtran、Gifsicle。

3. 马克鳗：国人开发的一款免费标注工具

4. [Dash](http://kapeli.com/dash)：API文档查询及代码片段管理工具

5. 蒲公英：应用的内测分发工具，类似苹果的TestFlight

6. 命令行工具
  * [nomad](http://nomad-cli.com)：一个方便操作苹果开发者中心的命令行工具，利用它可以做的事情包括方便地添加测试设备、更新证书文件、增加App ID、验证IAP的凭证等。。［有点意思］
  * [xctool](http://github.com/facebook/xctool)：脸书开源的一个iOS编译和测试的命令行工具，可以使用xctool在命令行进行编译和单元测试，然后将测试结果集成到Jenkins中＝自动化持续集成。
  * [appledoc](https://github.com/tomaz/appledoc)：从源代码中抽取文档的工具。

### 理解内存管理

* 引用计数
  > 由于引用计数简单有效，除了Objective-C语言外，微软的COM（Component Object Model)、C++1（C++11提供了基于引用计数的智能指针share_ptr）等语言也提供了基于引用计数的内存管理方式。

* 我们为什么需要引用计数
  > 在函数内使用一个临时对象，通常是不需要修改它的引用计数的。
  > 引用计数真正派上用场的场景是在面向对象的程序设计架构中，用于对象之间传递和共享数据。

* 不要向已经释放的对象发送消息
  > 调试Xcode中，有僵尸对象检查

* 循环引用（reference cycles）问题
  > 弱引用（weak reference）

* 使用Xcode检测循环引用！！Instruments！！

* Core Foundation 对象的内存管理
  > 对底层Core Foundation对象的内存管理，手动
  > API：CFRetain, CFRelease
  > ARC下，我们有时需要将一个Core Foundation对象转换成一个Objective-C对象，这个时候需要告诉编译器，转换过程中的引用计数需要如何调整。这就引入了几个关键词
  >> __bridge： 只做类型转换，不修改相关对象的引用计数，原来的Core Foundation对象在不用时，需要调用CFRelease方法。__
  >> __bridge_retained：类型转换后，将相关对象引用计数加 1，原来的Core Foundation对象不再用时，需要调用CFRelease方法。__
  >> __bridge_transfer：类型转换后，将该对象的引用计数交给ARC管理，Core Foundation对象在不用时，不再需要调用CFRelease方法。__
  我们根据具体的业务逻辑，合理使用上面的三种转换关键字，就可以解决Core Foundation对象与Objective-C对象相对转换的问题。

### 掌握GCD
  > 代码可读性

* 使用GCD
  > block定义：简单的block定义有点像函数指针，差别是用 ^ 替代了函数指针的 * 符号。

* 系统提供的dispatch方法

* 后台运行
  >


### 使用UIWindow


### 动态下载系统提供的多种中文字体（略）

### 使用应用内支付

### 基于UIWebView的混合编程

### 安全性问题


### 基于CoreText的排版引擎

### 实战技巧

1. App Store 与审核
