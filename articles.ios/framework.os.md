## 系统框架介绍

参考链接：

1. [iOS的主要框架介绍](http://blog.csdn.net/yuhuangc/article/details/7575519)

2. [IOS 整体框架类图值得收藏](http://www.cnblogs.com/ygm900/p/3599081.html)

3. [苹果iOS操作系统整体架构层次讲解](http://www.kejixun.com/article/201308/17085.html)

4. [IOS框架和服务](http://blog.csdn.net/GooHong/article/details/28911301)

### 目录
* [1. 系统框架分层](#1)
* [2. Foundation框架](#2)
* [3. UIKit框架](#3)
* [4. Core Data](#4)
* [5. Core Graphics](#5)
* [6. Core Animation](#6)
* [7. OpenGL ES](#7)
* [8. Cocoa对象](#8)

<h3 id="1">系统框架分层</h3>

1. [什么是框架](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/library.framework.md)

2. 具体的分层：iOS的系统架构分为四个层次，自底向上依次是：核心操作系统层（Core OS layer）、核心服务层（Core Services layer）、媒体层（Media layer）和可触摸层（Cocoa Touch layer）

    * 可触摸层的上层，则是应用开发层（application）。
    * Core OS 层 包含核心部分、文件系统、网络基础、安全特性、能量管理和一些设备驱动，还有一些系统级别的API。它可以直接和硬件设备进行交互。作为app开发者不需要与这一层打交道。
    * Core Services 层 提供核心服务，例如字符串处理函数、集合管理、网络管理、URL处理工具、联系人维护、偏好设置等。
    * Media 层 是媒体层，通过它我们可以在应用程序中使用各种媒体文件，进行音频与视频的录制，图形的绘制，以及制作基础的动画效果。
    * Cocoa Touch 是可触摸层，这一层为我们的应用程序开发提供了各种有用的框架，并且大部分与用户界面有关，本质上来说它负责用户在iOS设备上的触摸交互操作。

3. [核心操作系统层] 位于iOS系统架构最下面的一层是核心操作系统层，它包括内存管理、文件系统、电源管理以及一些其他的操作系统任务。它可以直接和硬件设备进行交互。核心操作系统层包括以下这些组件：

    * OS X Kernel
    * Mach 3.0
    * BSD
    * Sockets     
    * Power Management  
    * File System
    * Keychain     
    * Certificates   
    * Security
    * Bonjour

4. [核心服务层] 我们可以通过它来访问iOS的一些服务。它包括以下这些组件：

    * Collections     
    * Address Book   
    * Networking
    * File Access     
    * SQLite    
    * Core Location
    * Net Services    
    * Threading    
    * Preferences
    * URL Utilities

5. [媒体层] 通过它我们可以在应用程序中使用各种媒体文件，进行音频与视频的录制，图形的绘制，以及制作基础的动画效果。它包括以下这些组件：

    * Core Audio     
    * OpenGL    
    * Audio Mixing
    * Audio Recording   
    * Video Playback   
    * JPG，PNG，TIFF
    * PDF     
    * Quartz    
    * Core Animation
    * OpenGL ES

6. [可触摸层] 这一层为我们的应用程序开发提供了各种有用的框架，并且大部分与用户界面有关，本质上来说它负责用户在iOS设备上的触摸交互操作。它包括以下这些组件：

    * Multi-Touch Events  
    * Core Motion   
    * Camera
    * View Hierarchy   
    * Localization   
    * Alerts
    * Web Views     
    * Image Picker   
    * Multi-Touch Controls

整体架构图：
    ![architect 1](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/architect_1.jpg)

3. 如下图：各个框架对应IOS系统里特定的一层，上层基于下层，更高层次的框架是对底层框架基于对象的抽象。应该尽量使用上层的框架来代替下面的框架。

  ![pic 1](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/system_framework.jpg)

4. Cocoa
    * 建立初步印象？Cocoa是苹果公司为Mac OS X所创建的原生面向对象的API，是Mac OS X上五大API之一（其它四个是Carbon、POSIX、X11和Java）（有点类似windows里MFC，.net framework，Cocoa Touch是iOS的）

    * 它是什么？Cocoa是OS X和 iOS操作系统的程序的运行环境。
是什么因素使一个程序成为Cocoa程序呢？不是编程语言，因为在Cocoa开发中你可以使用各种语言；也不是开发工具，你可以在命令行上就可以创建 Cocoa程序。Cocoa程序是由一些对象组成，而这些对象的类最后都是继承于它们的基类 NSObject，都是基于Objective-C运行环境的。

    *

<h3 id="2">Foundation框架</h3>

1. Foundation框架为所有的应用程序提供基本系统服务，UIKit和其它的框架都是建立在Foundation框架上面的。Foundation框架是用Object-C对Core Foundation框架里许多特性的封装。

2. 创建和管理集合，比如数组和字典
    * [NSMutableArray](https://developer.apple.com/library/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSMutableArray_Class/)
    * [NSMutableDictionary](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSMutableDictionary_Class/)
3. 访问存储在应用程序里的图片和其它资源
    * [NSFileManager](https://developer.apple.com/library/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSFileManager_Class/)
4. 创建和管理字符串
    * [NSMutableString](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSMutableString_Class/)
5. 提交和接收通知
    * [NSNotification](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSNotification_Class/)
6. 创建日期和时间对象
    * [NSDate](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSDate_Class/)
7. 自动发现IP网络上的设备
    * [？？？？？这是什么？？？]
8. 操作URL流
    * [NSUrl](https://developer.apple.com/library/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSURL_Class/)
    * [NSUrlSession](https://developer.apple.com/library/prerelease/watchos/documentation/Foundation/Reference/NSURLSession_class/)
    * [NSUrlCache]
    * [NSUrlProtocol]
    * [NSUrlRequest]
    * [NSUrlResponse]
    * [NSUrlConnection]
    * [NSURLProtectionSpace]
    * [NSURLCache]
9. 执行异步代码
    * [GCD]
    * [NSOperation]

10. 三个图，包括了Foundation所以的类，图中灰色的是iOS不支持的，灰色部分是OS X系统的：

    ![Foundation 1](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/Foundation_1.jpg)
    ![Foundation 2](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/Foundation_2.jpg)
    ![Foundation 3](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/Foundation_3.jpg)

将上图Foundation框架中的类进行逻辑分类如下：
> 值对象
> 集合
> 操作系统服务 包括下面三个：文件系统和URL
    进程间通讯。 这个范畴中的大部分类代表不同的系统端口、套接字、和名字服务器，对实现底层的IPC很有用。NSPipe代表一个BSD管道，即一种进程间的单向通讯通道。
    线程和子任务。 NSThread类使您可以创建多线程的程序，而各种锁（lock）类则为彼此竞争的线程在访问进程资源时提供各种控制机制。通过NSTask，您的程序可以分出一个子进程来执行其它工作或进行进度监控。
> 通知
> 归档和序列化
> 表达式和条件判断
> Objective-C语言服务

<h3 id="3">UIKit框架</h3>

1. UIKit框架提供创建基于触摸用户界面的类

      所有的iOS应用程序都基于UIKit，你不能是应用程序脱离这个框架。UIKit提供了在屏幕上绘制的机制，捕获事件，和创建通用用户界面元素。UIKit也通过管理显示在屏幕上的组件来组织复杂的项目。

2. 构建和管理你的用户界面
    * [UIView]
    * [UIViewController]
    * [UIWindow]
3. 捕获触摸和基于移动的事件
    * [UIGestureRecognizer]
4. 呈现文字和web内容
    * [UITextView]
    * [UIWebView]
5. 优化你的多任务程序
    * ???
6. 创建定制的用户界面元素
    * [Xib & Storyboard]

7. 用法
应用程序可以通过三种方式使用UIKit创建界面
  在用户界面工具（interface Buidler）从对象库里 拖拽窗口，视图或者其他的对象使用。
  用代码创建
  通过继承UIView类或间接继承UIView类实现自定义用户界面

8. 上图
    ![UIKit architect](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/uikit.jpg)

在图中可以看出，responder类是图中最大分支的根类，UIResponder为处理响应事件和响应链定义了界面和默认行为。当用户用手指滚动列表或者在虚拟键盘上输入时，UIKit就生成时间传送给UIResponder响应链，直到链中有对象处理这个 事件。相应的核心对象，比如：UIApplication  ，UIWindow，UIView都直接或间接的从UIResponder继承。

<h3 id="4">Core Data</h3>

1. Core Data框架管着理应用程序数据模型
         Core Data提供对象的管理，使用Core Data，你可以创建模型对象，并管理这些对象。你管理这这些对象间的联系并修改数据。Core Data提供的内建SQLlite技术可以高效的管理数据。

2. 在库里存储和接收对象
???
3. 提供基本的undo/redo
???
4. 自动验证属性值
???
5. 过滤、分组和优化内存中的数据
???
6. 用[NSFetchedResultsController]管理表视图中的结果
???
7. 支持基于文档的应用程序
???

<h3 id="5">Core Graphics</h3>

1. Core Graphics框架帮助你创建图形
       高质量的图形对于所有的iOS应用程序都是很重要的。在iOS中最简单且最快捷的创建图形的方式是使用UIKit框架提供的基于预渲染图形的视图和控件，然后让UIKit和iOS完成绘制。但是当你需要创建复杂的图形时，Core Graphics则提供了更底层的库来帮助你。

2. 创建基于路径的绘图
3. 抗锯齿渲染
4. 添加梯度、图片和颜色
5. Use coordinate-space transformations.
6. 创建、显示和分析PDF文档

<h3 id="6">Core Animation</h3>

1. Core Animation允许你创建高级的动画和虚拟效果
           UIKit提供建立在Core Animation之上的动画。如果你需要比UIKit能力更高级的功能，可以直接使用Core Animation。Core Animation接口包含在Quartz Core框架里。使用Core Animation可以创建嵌套的对象，并且可以对它们操作、旋转、缩放和转换。使用Core animation，你可以创建动态的用户界面而不用使用更底层的图形API，如OpenGL ES。

2. 创建定制动画
3. 添加定时函数和图形
4. 支持帧动画
5. Specify graphical layout constraints.
6. Group multiple-layer changes into anatomic update.

<h3 id="7">OpenGL ES</h3>

1. OpenGL ES 框架提供2D和3D绘图工具
OpenGL ES支持2D和3D绘图，Apple的OpenGL ES实现通过硬件提供了高速的全屏游戏式的应用程序。

2. 创建2D和3D图形
3. 创建更复杂的图形，比如数据虚拟化、模拟飞行，或者视频游戏
4. 访问底层图形设备

<h3 id="8">Cocoa对象</h3>

1. Objective-C是面向对象的语言
  Objective-C和Java、C++一样，有封装，继承，多态，重用。但是它不像C++那样有重载操作法、模版和多继承，也没有Java的垃圾回收机制。

2. Objective-C的优点
  Objective-C语言有C++、Java等面向对象的特点，那是远远不能体现它的优点的。Objective-C的优点是它是动态的。动态能力有三种：
  动态类-运行时确定类的对象
  动态绑定-运行时确定要调用的方法
  动态加载--运行时为程序加载新的模块

3. 根类 NSObject
  NSObject是大部分Objective-C类的根类，它没有父类。其它类继承NSObject，访问Objective-C运行时系统的基本接口，这样其他类的实例可以获得运行时的能力。
