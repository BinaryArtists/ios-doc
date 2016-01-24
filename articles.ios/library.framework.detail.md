## 系统框架细谈

参考链接：

  1. [IOS框架和服务](http://blog.csdn.net/goohong/article/details/28911301)

### 目录
  * [1. Cocoa Touch Layer（触摸UI层）](#1)
  * [2. MediaLayer（媒体层）](#2)
  * [3. CoreServices Layer（核心服务层）](#3)
  * [4. Core OS Layer（核心OS层）](#4)

<h3 id="1">Cocoa Touch Layer（触摸UI层）</h3>

CocoaTouch Layer
包含创建ios应用关键的框架。该层包含的框架定义应用的外观，也提供基本的应用基础和关键的技术支持，例如多任务、触摸输入、推送通知和许多其它的高级系统服务。在开发应用时，应当首先研究该层的技术和技术看是否能够满足需要。

* Cocoa Touch Layer包含如下关键技术

  1. AirDrop

  AirDrop允许用户与附近设备共享图片、文档、urls链接以及其它种类的数据。

  2. Text Kit

  TextKit是处理文本和排版的一个全功能、高级别的类集合。使用Text Kit你能在段落、列或者页上对带有风格的文本进行布局；也能在任意区域（如图形）周围布局流动的文本；还能用它来管理多种字体。

  开发应用时应该首先考虑使用Text Kit来进行文本呈现，而不是Core Text。Text Kit与所有UIKit中的基于文本的控制集成允许应用更容易地创建、编辑、显示和存储文本。

  3. UIKit Dynamics

  用来为符合UIDynamicItem协议的UIView对象或其它对象规定动画行为。通过在应用的UI中集成真实世界行为和特性进，动画行为为应用提供了一种增强用户体验的方式。

  4. Multitasking

  在ios中多任务用来设计来使电池使用时间最大化。

  5. Auto Layout

  自动布局帮助你使用非常少的代码来建立动态接口。
  使用AutoLayout定义如何在用户接口上布局元素的规则，这些规则表达了视图类之间的关系，如规定一个按钮总是处于它的父窗口的左边缘20个点。
  在Auto Layout 中使用的实体是被称为constraints的Objective-C对象。

  6. Storyboards

  串联图 是设计应用用户接口的推荐方式。串联图让你在一个地方就能够设计全部的用户接口，方便在一个位置看到所有的视图和视图控制器以及理解它们是如何一起工作的。串联图的一个重要的部分是定义segues（segues是从一个视图控制器到另一个的转换）。这些转换代表用户接口之间的交互。你可以使用XCOE来可视的定义这些转换或者通过编程启动它们。

  你能使用一个单串联图文件来存储所有的应用视图控制器和视图，或者使用多个视图串联图文件来组织用户接口。
  在应用建立时间，Xcode读取串联图文件的内容并把它分成多个能独立加载的离散的片断，以便获得更好的性能。UIKit框架提供了相应的类来从程序中存取一个串联图的内容。

  7. UI State Preservation

  UI状态保存能够使应用表现的一直运行，从而为用户提供无缝的体验。如果系统遇到内存压力，系统可能安静地强制停止一个或多个后台应用。

  当应用从前台移到后台时，该服务能保存应用的视图和视图控制器的状态。在下次应用重新启动时，能够使用先前保存的状态信息来恢复视图和视图控制器到它们先前的配置，使应用表现得好像一直在运行。

  8. Apple Push Notification Service

  苹果的推送通知服务提供了一种提示用户关于新信息的方式，即使应用当前不在激活运行状态。

  使用该服务，你能推送文本通知，在应用图标上增加一个标记或者在任意时间触发声音提示。

  这些消息让用户知道他们应该打开应用来接收相关信息。自Ios7开始,你甚至能推送无声的通知来让应用知道有了新的内容可以下载。

  为了使用IOS应用的推送通知，用户需要做两部分的工作。首先应用必须登记该通知服务以及在通知被提交时处理相关的通知数据。第二，你必须提供一个服务端的进程来产生通知。

  服务端的进程可以使用你自己的本地服务器或者使用苹果的推送通知服务。

  9. Local Notifications

  本地通知作为推送通知机制的补充，可以给应用提供一种不依赖外部服务器产生本地通知的方式。

  运行在后头的应用能使用本地通知作为当重要的事件发生时引起用户注意的一种方式。例如，运行在后台的导航应用能使用本地通知来提示用户什么时间该转弯了。

  应用也能调度本地通知在将来的时间提交以及使那些通知在应用不运行也能被提交。

  本地通知的一个优点是它们与你的应用是独立的。在一个通知已被调度，系统管理它的提交。另外当通知被提交时你的应用甚至不必运行。

  10. Gesture Recognizers

  手势识别用来检测通常类型的手势。由于手势识别使用与系统检测手势相同的试探方法，因此手势识别为应用提供了一个一致的行为。为了使用它，你能在你的视图上附加手势识别功能和并给它提供一个在手势出现时要执行的方法。

  手势识别跟踪原始的触摸事件和确定它们什么时候与想要的手势匹配。

  11. System View Controllers

  许多系统框架为标准的系统接口定义了视图控制器。只要有可能，为了呈现一致的用户体验，就应该使用系统提供的视图控制器而不是创建一个新的。

* CoCoa Touch层包含如下框架：

  1. Address Book UI Framework（地址本UI框架）

  该框架提供一个面向对象的编程接口。用来显示标准的系统接口，来创建新的联系人和编辑和选择已存在的联系人。

  2. Event Kit UI Framework（月历事件UI框架）

  该框架提供一个视图控制器来呈现标准的系统接口，来观察和编辑月历相关的事件。EventKit UI Framework基于Event Kit framework框架。

  3. Game Kit Framework（游戏工具框架）

  该框架实现对游戏中心的支持，让用户能够在线共享他们的游戏相关的信息。

  4. iAd Framework（iAD框架）

  该框架用来在应用中提供广告条。
  当你想要显示广告时，广告条与用户UI上的标准的视图进行合并。
  这些视图与苹果的iAd服务一起工作，自动处理、加载和呈现富媒体广告以及应答在那些广告条上的点击等所有相关的工作。

  5. Map Kit Framework（地图工具框架）

  MapKit提供与应用的UI组合的一个可滚动的地图。

  除了显示一个地图，你能使用该框架接口来定制地图的内容和外观，也能使用注解来标记感兴趣的点，也能使用定制的内容来与地图内容叠置。例如，你可以在地图上来画一条公交路线，或者使用注解来高亮显示附近的商店和餐馆。

  除了显示地图，MapKit框架还能与地图应用以及苹果的地图服务器集成来为用户指引方向。

  地图应用能够给任意支持方向的应用提供方向的代理。如提供特定类型方向的应用，例如一个显示地铁路线的应用，能登记请求接收地图应用提供的方向。
  应用也能向苹果的服务器请求步行或驾驶方向，并与他们定制的方向的路径信息混合来为用户提供完整的点到点体验。

  6. Message UI Framework( 消息UI框架 )

  该框架用来在应用中提供编辑邮件和sms消息的支持。
  编辑支持包括一个呈现到你的应用的视图控制器接口，并能设置这个视图控制器的一些区域，如接收人、主题、邮件主体和邮件想包括的任意附件。
  在呈现视图控制器后，也能为用户提供一个在发送邮件之前可以编辑邮件的选项。

  7. UIKit Framework

  该框架提供实现图形和事件驱动的应用的至关重要的基础。包括：   

      a. 基本的应用管理和基础设施，包括应用的主循环；

      b. 用户接口管理，包括对storyboards和nib文件的支持；

      c. 一个用来封装用户UI内容的视图控制器模式；

      d. 标准系统视图和控制对象；

      e. 提供处理触摸和运动事件的支持；

      f. 支持包括与iCloud集成功能的文档模式；

      g. 图形和窗口支持，包括支持外部显示器；

      h. 多任务支持；

      i. 打印支持；

      j. 定制标准UIKit控制的外观；

      k. 支持文本和web内容；

      l. 剪切、复制、粘贴的支持；

      m. 支持动画UI；

      n. 通过url语义和框架接口与系统提供的其它应用集成的能力；

      o. 对有障碍用户的可存取性的支持；

      p. 支持ApplePush Notification服务；

      q. 本地通知调度和提交；

      r. pdf 创建；

      s. 支持定制像系统键盘行为一样的用户输入视图；

      t. 支持创建与系统键盘交互的定制的文本视图；

      u. 支持通过email,Twitter, Facebook和其它服务共享内容。

也支持一些设备特定功能的集成，例如
      a. 内建的摄像机；
      b. 用户的图片库；
      c. 设备名和模式信息；
      d. 电池状态信息；
      e. 接近传感器信息；
      f. 来自附件耳机的远程控制信息

<h3 id="2">MediaLayer（媒体层）</h3>

媒体层包含在应用中实现多媒体体验的图形、声音、视频技术和框架。使用这层的技术可以使你容易的建立更加好看和好听的应用。

* 包含的关键技术

  1. 图形技术

  高质量的图形是所有应用的重要的组成部分。IOS提供了许多帮助你定制艺术和图形屏幕的技术。IOS图形技术为其提供了广泛的支持，并可以与UIKit视图架构无缝工作。

  你能使用标准的视图来快速提交高质量的接口，或者使用本层的图形技术创建你自己的定制视图来提交一个更加丰富的图形体验。

    a. UIKit graphics
      UIKit定义的绘制图像和Bézier路径，以及动画视图内容的高级别技术。
      UIKit视图提供快速和有效的方式来呈现图像和文本内容。
      UIKIT视图也能通过显示和使用UIKitdynamics技术进行动画，并为用户提供反馈，促进用户交互。

    b. CoreGraphics 框架

      CoreGraphics也称作Quartz，是对定制的2D向量和图像呈现提供支持的本地绘制引擎。
      该框架提供的引擎虽然没有OpenGLES引擎速度快，但该框架能够很好地适合于呈现定制的2d图形和动态图像。

    c. CoreAnimation框架

      CoreAnimation也是Quartz核心框架的一部分，是优化应用动画体验的基础技术。
      UIKit视图基于 Core Animation提供视图级别的动画支持。
      当你想对动画行为有更多控制时也能直接使用CoreAnimation。

    d. Core Image

      CoreImage提供非破坏的方式操作视频和静态图像。

    e. OpenGL ES及GLKit

      OpenGLES使用硬件加速接口来处理先进的2d 和3d 呈现。OpenGLES通常由游戏开发者或想实现沉浸式图像体验的开发者使用。

      OpenGLES框架提供对呈现过程的全部控制，以及提供创建平滑动画所需要的帧速。

      GLKit是一组Objective-C类，以便能够使用面向对象接口来提供OpenGL ES的强大能力。

    f. Text Kit和CoreText

      Text Kit是UIKit框架的家族，用来来执行最好的排面和文本管理。如果你的应用实现先进的文本操作，Text Kit提供与应用视图的无缝集成。

      CoreText是处理先进排面和布局的低级别的c语言框架。

    g. Image I/O

    ImageI/O提供读写大多数图像格式的接口。

    h. Assets Library

    AssetsLibrary框架让你存取用户的图片、视频和媒体。
    你想在应用中集成用户自己的内容时可以使用该框架。

  2. 声音技术

  声音技术工作于底层硬件之上，为用户提供更加丰富的声音体验。这些体验包括播放和记录高质量的声音、处理MIDI内容以及使用设备内建的声音 等能力，

    a. Media Player framework

      该框架是一个高级别的框架， 用来为用户提供对iTunes库存取的容易方式，也提供对播放轨迹和播放列表的支持。
      当你想快速在应用中集成声音以及不需要控制播放行为时可以使用该框架。

    b. AV Foundation

      AVFoundation是管理声音以及视频播放和记录的面向对象接口。
      在记录声音和想对声音播放过程有更好的控制时可以使用该框架。

    c. OpenAL

      OpenAL是一个提供位置音效的跨平台的工业标准技术和接口。
      游戏开发者经常使用该技术来提供高质量的声音。

    d. Core Audio

      Core Audio是一组简单和智能的接口来记录和播放声音以及MIDI内容。
      在需要对声音有更好控制时使用该框架。

  3. 视频技术
    视频技术提供管理应用中的静态视频内容或者播放来自Internet的视频流的支持。
    对于带有适当的记录硬件的设备，该框架还能够记录视频以及与应用进行集成。

    a. UIImagePickerController

      UIImagePickerController是一个选择用户媒体文件的UIKit视图控制器。

    b. Media Player

      MediaPlayer框架提供一组呈现视频内容的简单易用的接口，该框架支持全屏和小窗口视频播放，也为用户提供可选的播放控制。

    c. AVFoundation

      AVFoundation提供先进的视频播放和记录能力。
      在需要对视频呈现和记录有更多的控制时使用该框架，例如在实时应用中分层显示实时视频和应用提供的其它内容。

    d. CoreMedia
      CoreMedia框架为操作媒体定义低级别的数据类型和接口。
      当你需要对视频内容有无比的控制时可以使用该框架。

  4. AirPlay技术

    AirPlay让应用串流声音和视频内容到Apple TV或者串流声音内容到第三方扬声器和接收器。

    AirPlay内建于许多框架，包括UIKit、Media Player、AVFoundation、Core Audio。因此在大多数情况你不需要为了支持它做任何事。在使用那些框架时，当播放内容时自动获得AirPlay支持。当用户选择使用AirPlay播放内容时系统自动进行路由。

* 包含的框架

MediaLayer提供如下框架和服务。

  1. Assets Library 框架

    AssetsLibrary 框架(AssetsLibrary.framework)提供对用户设备上图片应用管理的图片和视频的存取。
    使用该框架来存取用户保存的图片相册或导入到设备的任意相册中的图片，你也能保存新的图片和视频到用户的图片相册。

  2. AV Foundation 框架

    AVFoundation 框架 (AVFoundation.framework)提供一组播放、记录和管理声音和视频内容的Objective-C类。

    当你想在应用的ui接口无缝集成媒体能力时使用该框架。

    你也能使用它来进行更先进的媒体处理，例如同时播放多个声音或者控制播放和记录过程的多个方面。

    该框架提供的服务包括:    
      a. 声音会话管理，包括对系统声明你的应用声音能力；  
      b. 对应用媒体资源的管理；
      c. 对编辑媒体内容的支持；
      d. 捕捉声音和视频的能力；
      e. 播放声音和视频的能力；
      f. 轨迹管理；
      g. 媒体元数据的管理；
      h. 立体拍摄；
      i. 声音之间的精确同步；
      j. 提供一个确定声音文件细节内容的Objective-C接口，例如数据格式，采样率，通道数；
      k. 通过AirPlay串流内容。   

  3. Core Audio 框架
  Core Audio 是一个对声音处理提供本地支持的框架家族。这些框架支持声音的产生、记录、混合和回放。你也能使用这些接口处理MIDI内容以及串流声音和MIDI内容到其它应用。

  Core Audio框架包括如下框架：
    a. CoreAudio.framework
      定义Core Audio框架使用的所有数据类型。

    b. AudioToolbox.framework
      提供声音文件和声音流的播放和记录服务。也提供管理声音文件，播放系统警告声音，在某些设备上触发震动的支持。

    c. AudioUnit.framework
      提供使用内建声音单元。也提供使你的应用的声音内容作为对其它应用可视的声音组件的支持。

    d. CoreMIDI.framework
      提供与MIDI设备通讯的标准方式，包括硬件键盘和合成器。你使用这个框架来发送和接收MIDI消息以及与通过dock连接器或网络连接到IOS设备的MIDI外设交互。

    e. MediaToolbox.framework
      提供对声音tap接口的存取。

 4. Core Graphics 框架
  CoreGraphics.framework包含Quartz 2D绘制api。

  Quartz是一个原先用在OS X的先进的、向量绘制引擎。Quartz支持路径绘制，抗锯齿呈现，剃度，图像，颜色，坐标空间转换以及pdf 内容创建、显示和分析等功能。

  虽然这个api是C-based接口，但它使用了面向对象抽象来表现基本的绘制对象，因此使它容易存储和重用图形内容。

  5. Core Image 框架
    CoreImage 框架(CoreImage.framework)提供一组强大的内建过滤器来操作视频和静态图像。
    你能在触摸弹起、纠正图片以及面部和特征检测等许多方面使用这些内建的过滤器。这些过滤器的先进特点是它们操作在非破坏方式，即原先的图像不被改变。

    这些过滤器针对底层硬件进行了优化，因此它们是快速和有效的。

  6. Core Text 框架
    CoreText 框架 (CoreText.framework)提供一个对文本进行布局和字体处理的简单的、高性能的C-based接口。
    该框架用在不使用TextKit但仍想获得在字处理应用中发现的先进文本处理能力。
    该框架提供了一个智能的文本布局引擎，包括在其它内容周围环绕文本的能力，它也支持使用多种字体和呈现属性的先进的文本风格。

  7. Core Video 框架
    CoreVideo 框架 (CoreVideo.framework)为Core Media框架提供缓冲和缓冲池支持。多数应用从不直接使用该框架。

  8. Game Controller 框架
    GameController 框架 (GameController.framework)让你在应用中发现和配置针对iPhone/iPod/iPad设备的游戏控制器。

    游戏控制器可以是物理连接到iOS设备或者是通过蓝牙无线连接。GameController框架当控制器可获得时通知你的应用让应用可以规定哪个控制器输入与你的应用相关。

  9. GLKit 框架
    GLKit框架 (GLKit.framework)包含一组简化创建OpenGLES应用的Objective-C based 单元类。
    GLKit支持应用开发的四个关键领域
       1)GLKView和GLKViewController类提供一个OpenGLES视图和其呈现循环的标准实现。
          OpenGLES视图代表应用管理底层的framebuffer对象。应用只需在视图上绘制。
       2） GLKTextureLoader类提供在你的应用中使用图像转换和加载线程，允许应用自动加载纹理图像到应用的上下文。
  能够异步或同步加载纹理。当异步加载纹理时，应用应提供一个完成处理块，该处理块在纹理加载进应用上下文时被调用。
       3）GLKit框架提供向量、矩阵和3d 旋转以及提供OpenGLES 1.1上的矩阵。
       4）GLKBaseEffect,GLKSkyboxEffect,和GLKReflectionMapEffect类实现给通用图形操作提供可配置的图形着色。尤其GLKBaseEffect类实现了OpenGL ES 1.1规范上的光亮和材质模式，简化了移植一个应用从OpenGL ES 1.1到OpenGL ES最后版本的努力。

  10. Image I/O 框架
    ImageI/O 框架(ImageIO.framework)提供输入和输出图像数据和图像元数据的接口。

    该框架利用CoreGraphics数据类型和功能，并支持在ios 上所有的可获得的标准的图像类型。你能使用这个框架存取Exif和IPTC元数据属性。

  11. Media Accessibility 框架
    MediaAccessibility 框架 (MediaAccessibility.framework)管理媒体文件中closed-caption内容的呈现。

    该框架与新的设置配合工作可以让用户决定是否允许closed-caption显示。

  12. Media Player 框架
      MediaPlayer 框架(MediaPlayer.framework)提供应用中播放声音和视频的高级别支持。能够使用该框架做如下工作：

        1） 播放视频到用户屏幕或通过AirPlay到另外的设备屏幕。能够全屏幕播放视频或以可改变视图大小的方式播放。

        2）存取用户的iTunes音乐库。能够播放音乐轨迹和播放列表、搜索音乐、给用户提供一个媒体picker呈现接口。

        3）配置和管理电影的回放。

        4） 在锁定屏幕和app 切换窗口上显示NowPlaying信息。当内容通过AirPlay提交时还能显示到AppleTV上。

        5）检测视频通过AirPlay被串流的时间。
  13. OpenAL 框架
      OpenAudio Library (OpenAL)接口是用来在应用中提供位置音效的跨平台的标准。
      能够使用该接口在游戏和其它需要位置音效输出的程序中实现高性能、高质量的声音。
      因为OpenAL是跨平台的标准，在iOS使用OpenAL编写的代码能够容易地移植到许多其它平台。

  14. OpenGL ES 框架
      OpenGLES 框架 (OpenGLES.framework)提供绘制2d和3d内容的工具， 它是一个C-based的框架。
      该框架以最接近设备硬件的方式为全屏沉浸式应用例如游戏提供细粒度的图形控制和高的帧率。
      你能够与EAGL配合使用这个框架，为OpenGL ES 绘制调用和UIKit的本地窗口对象之间提供接口。
      该框架支持OpenGLES 1.1, 2.0, 3.0规范。2.0规范增加了片段和顶点着色的支持，3.0规范增加了更多的功能，包括多个呈现目标和变换反馈。
  15. Quartz Core 框架
      QuartzCore 框架(QuartzCore.framework)包含Core Animation接口。
      Core Animation是一个先进的复合技术，使用它能容易创建快和有效的view-based的动画。
      复合引擎利用底层硬件来有效的实时操作视图内容。
      只需规定动画的起始点，CoreAnimation做剩下的工作。
      因为Core Animation内嵌在UIView架构的底层，因此它总是可用的。

  16. Sprite Kit 框架
       SpriteKit 框架 (SpriteKit.framework)框架为2d和2.5d游戏提供硬件加速的动画系统。
       SpriteKit提供大多数游戏需要的基础，包括一个图形引擎和动画系统，声音播放支持，一个物理仿真引擎。  使用SpriteKit不需你自己创建这些事情，使你聚焦在内容设计和内容的高级别的交互上。
       在Sprite Kit应用中内容组织为场景。一个场景包括纹理对象，视频，路径图形，核心图像过滤器和其它的特效。SpriteKit利用这些对象，确定这些对象到屏幕上的最有效的方式。当在场景中到了动画内容的时刻，你能使用SpriteKit来显式规定你想执行的行动或使用物理仿真引擎来为那些对象定义物理行为（例如重力、引力或排拆力）。
        除了SpriteKit框架，也有其它Xcode工具来创建颗粒发射效果和纹理图。你能使用Xcode工具来管理应用资源和快速地更新Sprite Kit场景。

<h3 id="3">CoreServices Layer（核心服务层）</h3>

CoreServices Layer 包含应用需要的基础的系统服务。这些服务中的核心是CoreFoundation和Foundation框架，定义了所有应用使用的基本类型。
该层也包含独立的技术来支持一些其它功能， 例如位置、iCloud、社交媒体和网络。

* 包含的高级功能：
     Peer-to-Peer Services（点到点服务）
         这个Multipeer Connectivity框架提供通过蓝牙进行p2p连接的能力。
         你能使用p2p连接来启动与附近设备的通讯会话。
         虽然p2p连接主要用在游戏中，你也能在其它类型的应用中使用这个功能。
    iCloud Storage（云存储）
       iCloud存储让应用把用户文档和数据写到一个中心位置，用户然后能从他们的计算机和ios 设备存取这些数据。
      使用iCloud可以使用户文档无所不在，意味着用户能从任何设备阅读或编辑那些文档，而不需要显式的同步或文件传输。存储文档到用户的iCloud账户也为用户提供了一层安全。即使用户的设备丢失，那些设备上的文档如果已经保存到iCloud就不会丢失。
      应用能以两种方式使用 iCloud存储，每一种有不同的使用意图：
      1） iCloud文档存储。
       可以使用这个功能在用户的iCloud账户存储用户文档和数据。
      2）iCloud键值存储。
      使用这个功能在应用之间共享数据。
       大多数应用使用iCloud文档存储来共享来自用户账户的文档。使用iCloud文档存储用户关心的是文档能否能够在设备之间共享以及他们是否能够从一个给定设备查看和管理那些文档。
      相對的，iCloud键值存储是应用与应用的其它实例共享小量数据（几十k字节）的方式，应用应当用它存储非紧急的应用数据，例如设置。
   Automatic Reference Counting（自动引用计数）
         AutomaticReference Counting（ARC)是一个编译级别的功能，用它来简化Objective-C对象生命周期过程的管理，以此代替用户必须记住什么时候应该保持和释放对象。
        ARC评估对象的生命周期需求和自动在编译时间插入适当的方法调用。
        ARC用来代替ios 的早期版本中存在的传统的管理内存的编程模式。
        新创建的工程自动使用ARC。XCODE也提供了移植工具帮助你转换遗留的工程来使用ARC.
Block Objects（块对象）
      BlockObjects是一个能够与你的C或Objective-C代码集成的C语言的构造块。一个blockobject本质上是一个异步功能和相关的数据。在其它语言中有时也被称做closure或lambda。
     Blocks尤其用作回调或放在你需要一种容易的组合执行代码和相关数据方式的地方。
     在ios，通常在下面的场景使用Blocks：
     1）作为代理或代理方法的代替；
     2） 作为回调功能的代替；
     3）为某个一次性操作实现其完成处理函数；
     4）  在一个集合中的所有项上执行一个任务；
     5）与提交队列一起执行异步任务。
Data Protection（数据保护）
        DataProtection允许应用利用设备上已有的内建的加密方法来使用用户的敏感数据。
        当应用指定一个特定的文件被保护时，系统在磁盘上以加密格式存储该文件。当设备锁定时，该文件的内容不能被应用和任何潜在的侵入者存取。可是当设备由用户解锁时，一个解密key被创建允许你的应用存取那个文件。
         用户也可以使用其它级别的数据保护机制。
         实现数据保护需要你考虑如何创建和管理你想保护的数据。应用必须设计在数据的创建时间加密数据，以及当用户锁定或解锁设备时为存取条件改变做好准备。
File-Sharing Support（文件共享支持）
      File-SharingSupport使用户数据文件在iTunes 9.1和以后上可被其它应用获得。一个应用声明支持文件共享使它的/Documents目录下的内容对其它用户可获得。用户然后当需要时能够把文件从iTunes移进或移出应用的Documents目录。
      这个特征不允许应用与相同设备上的其它应用共享应用，这需要粘贴板或一个文档交互控制器对象。
     应用为了允许文件共享支持，需要做如下工作：
1、 在应用的Info.plist文件中增加UIFileSharingEnabled键，并设置其值为YES。
2)、在你的应用的Documents中放你想共享的文件；
3、当设备插进用户的计算机时，iTunes在选中设备的Apps标签下显式一个文件共享节；
4、用户然后能够增加文件到设备的文档目录或移动文件到桌面。
       支持文件共享的应用应该能够识别文件什么时候增加到其Documents目录和做出适当的应答。例如应用可以使任意新文件的内容可以从它的接口获得。也应该从不把Documents目录的文件列表呈现给用户来请求用户决定对那些文件做什么。
Grand Central Dispatch
      GrandCentral Dispatch（GCD)是一个BSD技术，应用可以用来管理其任务的执行。
      GCD与高优化的核组合成一个异步编程模式，来提供方便和更有效的对线程的替代。GCD也为许多低级别的任务提供一个方便的选择，例如读和写文件描述符，实现定时器和监视信号和处理事件。

In-App Purchase（应用内购买）
      In-App Purchase 提供在应用中销售应用特定的内容和服务以及来自iTunes的内容的能力。
       这个功能使用StoreKit框架实现，并提供使用用户的iTunes账号来处理金融方面的事务需要的基础。
     应用处理全部用户体验和供购买的内容及可获得服务的呈现。作为可下载的内容，你能把可下载的内容放到你自己的服务器或使用苹果的服务器。
SQLite
       SQLite库让你在你的应用中嵌入一个轻量级的sql数据库，而不需要运行一个分离的远程数据库服务进程。从你的应用，你能创建本地数据库文件，管理数据库表和表中的数据记录。
      SQLite库为通用功能使用设计，但已经被优化来提供对数据记录更快速的存取。
XML Support
       Foundation框架提供一个NSXMLParser类用来从一个xml文档中引出元素。
      操作xml内容的额外的支持由libxml2库提供支持。libxml2开源库让你快速地分析或写任意的xml数据和转换xml内容到html.
3.2 Core Services Frameworks（核心服务框架）
     Core Services Frameworks包含下面的一些框架。
     1）、Accounts Framework（帐户框架）
      Accounts框架 (Accounts.framework)为确定的用户账号提供单点登录模式。
      单点登录通过消除用户分离的多个账号需要的多次登录提示，来增强用户体验。它也通过为应用管理账号认证过程来简化开发模式。
     该框架需要与Social框架配合使用。
    2）Address Book Framework（地址本框架）
        AddressBook 框架(AddressBook.framework)提供可编程存取用户的联系人数据库的方式。
        如果应用使用联系人信息，你能使用该框架来存取和修改联系人信息。例如一个聊天应用可以使用该框架来引出可能的联系人列表，通过联系人列表来启动一个会话以及在特定视图显示那些联系人。
        重要提示：存取用户的联系人数据需要用户的明确的许可。应用因此必须准备好用户拒绝存取的情形。应用也鼓励提供Info.plist键来描述需要存取的原因。
  3）Ad Support Framework（广告支持框架）
       AdSupport 框架 (AdSupport.framework)提供存取应用用于广告功能的一个标识。
       该框架也提供一个指示用户是否选择广告跟踪的标志。应用在试图存取广告标识前需要度和判断这个标志。
 4）CFNetwork 框架
       CFNetwork框架 (CFNetwork.framework)是高性能的使用面向对象对网络协议进行抽象的一组C-based接口。这些抽象提供对协议栈细节的控制，使它容易使用低级别的构造例如BSDsockets。
       你能使用该框架简化与ftp或http服务器通讯或决定dnshosts的任务。使用CFNetwork 框架，你能：
1、使用BSD sockets。
2、使用SSL或TLS创建安全连接。
3、决定dnshosts。
4、与HTTP服务器、认证HTTP服务器、HTTPS服务器交互。
5、与FTP服务器交互。
6、发布、解决和浏览Bonjour服务。
               CFNetwork物理和理论上基于BSD sockets。
        5）Core Data 框架
                CoreData 框架 (CoreData.framework)框架是管理MVC应用中的数据模式的一种技术。
        CoreData框架打算在数据模式是高结构化的应用中使用。
      代替编程定义数据结构，在xcode中能够使用图形工具来建立一个表现你的数据模式的纲要。在运行时，你的数据模式实体的实例通过CoreData框架被创建、管理和获得。
           通过为你的应用管理其数据模式，CoreData大大减少了必须书写的代码量。CoreData也提供如下功能：
1、为优化性能在SQLite数据库中存储对象数据；
2、一个管理数据表视图结果的 NSFetchedResultsController类；
3、对基本的文本编辑之外的undo/redo的管理；
4、支持属性值的校验；
5、支持传播改变确保对象之间的关系保持一致性；
6、支持分组、过滤和在内存中优化数据。
如果你开始开发一个新应用或计划对已有应用进行大的更新，应该考虑使用CoreData。
6）Core Foundation 框架
      CoreFoundation 框架 (CoreFoundation.framework)是一组C-based接口，为ios应用提供基本的数据管理和服务功能。该框架包括如下支持：
  集合数据类型（数组、集合等等）；
  应用打包Bundles；
字符串管理；
日期和时间管理
原始数据块管理
Preferences管理；
URL和流操作；
线程
   9、端口和socket通讯。
      CoreFoundation框架与Foundation框架紧密相关，为相同的基本功能提供Objective-C接口。
        当你需要混合使用Foundation对象和Core Foundation类型时，你能利用两个框架之间存在的“toll-freebridging”。toll-free bridging”意味着你能可交换地在两个框架的方法和功能中使用一些CoreFoundation和Foundation类型。这个支持对许多数据类型可用，包括集合和字符串数据类型。
        每个框架的类和类型描述声明一个对象是否是toll-freebridged以及在是的情况下来标识它连接到什么对象。
7）Core Location 核心位置框架
            CoreLocation 框架  (CoreLocation.framework)为应用提供位置信息。该框架使用板上的GPS、蜂窝、或者Wi-Fi来定位用户的当前经度和纬度。
           你可在你的应用中集成该技术为用户提供位置信息。例如，你可实现一个基于用户的当前位置搜索附近餐馆、商店或者银行的应用。CoreLocation框架也提供如下能力：

          1） 在包括磁力计的ios设备上存取罗盘信息；

          2） 基于地理位置或蓝牙beacon进行区域监视；

          3） 支持使用蜂窝基站的低耗电的位置监视；

          4）与MapKit配合来增强在特定情景下的位置数据的质量，例如开车时。
8）Core Media Framework（核心媒体框架）
      CoreMedia 框架(CoreMedia.framework)提供由AV Foundation框架使用的低级别的媒体类型。大多数应用从不需要使用该框架，但少数需要更精确控制音视频内容创建和呈现的开发者可以使用它。
9）Core Motion Framework （核心运动框架）
         CoreMotion 框架 (CoreMotion.framework)提供一组接口来存取设备上可获得的运动数据。
       该框架支持使用一组新的block-based接口来存取原始和加工过的加速度计数据。对于带有陀螺仪的设备，你也能获得原始的陀螺仪数据和加工过的反应设备方向和旋转速度的数据。
         你能在游戏或其它使用运动作为输入或作为增强用户体验的方式的应用中使用加速度计和陀螺仪两种数据。对于带有计步硬件的设备，你能存取它的数据来跟踪健康相关的运动。
10）Core Telephony Framework（核心电话框架）
        CoreTelephony 框架 (CoreTelephony.framework)提供与蜂窝电话的通话相关的信息交互的接口。
        可以使用该框架来获得用户的蜂窝服务提供者的信息。对于对蜂窝call事件感兴趣的应用例如VoIP应用也能在那些事件出现时被通知。
11）Event Kit 框架
                EventKit 框架 (EventKit.framework)提供存取用户设备上的月历事件的接口。能够使用该框架来做如下事情：

         1） 获得用户月历上存在的事件和提示；

        2）增加事件到用户月历；

        3）为用户创建提示和使它们出现在提示应用中；

       4）为月历事件配置提示信号，包括设置提示信号应该什么时候触发的规则。       
     重要提示：存取用户的月历和提示数据需要用户的明确许可。应用因此必须准备好用户拒绝的情形，也鼓励应用在其Info.plist文件中提供一个描述需要存取原因的键。
12）Foundation框架
          Foundation框架 (Foundation.framework)提供Core Foundation框架提供的许多功能的Objective-C封装。该框架提供如下功能的支持：
  集合数据类型（数组、集合等等）；
  应用打包Bundles；
字符串管理；
日期和时间管理
原始数据块管理
Preferences管理；
URL和流操作；
线程和运行环；
Bonjour；
 通讯端口管理；
 国际化；
规则表达式匹配；
Cache支持。
13）JavaScript  核心 框架
       JavaScriptCore 框架 (JavaScriptCore.framework)为许多标准的JavaScript对象提供Objective-C语言的封装。使用该框架来执行JavaScript代码和分析JSON数据。
14）Mobile Core Services （移动核心服务框架）
       MobileCore Services 框架(MobileCoreServices.framework)定义在通用类型标识符（UTIs）中使用的低级别类型。
15）Multipeer Connectivity Framework（多方连接框架）
       MultipeerConnectivity 框架 (MultipeerConnectivity.framework)支持附近设备的发现，并与那些设备直接通讯（不需要Internet连接）。
       使用该框架能够与附近设备通讯、容易的创建多人会话、支持可靠地传输顺序和实时数据。
       该框架为发现和管理网络服务提供可编程和UI-based的选项。应用能在ui中集成MCBrowserViewController类来显示一个发现设备列表让用户选择。另外也能使用MCNearbyServiceBrowser类来可编程的查找和管理对方设备。
16）Newsstand Kit 框架
     Newsstand应用为用户提供了一个阅读杂志和报纸的中心位置。想通过Newsstand提供杂志和报纸内容的出版商能够使用NewsstandKit 框架(NewsstandKit.framework)创建它们自己的iOS应用，让用户启动新杂志和报纸新闻的后台下载。在启动下载后，系统处理下载操作和当内容可获得时通知应用。
17）Pass Kit 框架
       Passbook应用为用户提供了一个存储订货单、登机卡、入场券和商业折扣卡的位置。代替物理携带这些东西，用户现在能在IOS设备上存储它们，并和过去一样的方式使用。
       Pass Kit 框架 (PassKit.framework)提供把这些功能集成到你的应用的Objective-C接口。
      你能与web接口和文件格式信息组合使用该框架来创建和管理你们公司提供的电子入场券。
      电子入场券由你们公司的web service创建并通过email、Safari或定制的应用提交到用户的设备。电子入场券本身使用特殊的文件格式，在提交之前被加密签名。文件格式标识关于提供服务的相关信息以及用户知道是什么服务的信息。
         电子入场券也可以包含一个对卡进行校验的条码或其它信息，以便它能被兑换或使用。
18）Quick Look 框架
       QuickLook 框架(QuickLook.framework)提供了一个预览应用不直接支持的文件内容的接口。
        该框架主要打算用于应用从网络下载文件或处理来自不知道来源的文件的工作。
       在得到文件后，你能使用该框架提供的视图控制器来直接显示文件的内容。
19）Safari Services 框架
     SafariServices 框架 (SafariServices.framework)提供以可编程的方式增加URLs到用户的Safari的书签的支持。
20）Social Framework（社交框架）
      Social框架(Social.framework)提供一个简单的接口来存取用户的社交媒体账号。
      该框架取代Twitter框架并增加了其它社交账号，包括Facebook、Sina微博以及其它。
      应用能使用该框架提交状态更新和图像到用户账号。该框架与Accounts框架一起为用户提供单点登录并确保存取的用户账号是经过准许的。
21）Store Kit 框架
       StoreKit 框架 (StoreKit.framework)提供在ios应用中购买内容和服务的支持，也被称作应用内购买。
       例如，你能使用该功能来允许用户去锁另外的应用功能。或者如果你是一名游戏开发者，你能使用它来提供另外的游戏级别。在这两种情况，StoreKit框架处理事务的收入方面事务，包括通过用户的iTunes账号处理付费请求，给应用提供关于购买的信息。
        Store Kit聚集在事务的金融方面，确保事务正确和安全。你的应用处理事务的其它方面，包括购买接口的呈现和适当内容的下载（去锁）。
         工作的分工让你能够控制购买内容的用户体验。由你决定你想呈现给用户什么样的购买接口和什么时候那样做，你也决定你的应用最好的提交机制。
22）System Configuration Framework（系统配置框架）
       SystemConfiguration 框架(SystemConfiguration.framework)提供可达性接口，你能用它来确定设备的网络配置，也能使用该框架确定一个Wi-Fi或蜂窝连接是否在用以及一个特定的主机服务器是否能够存取。

<h3 id="4">Core OS Layer（核心OS层）</h3>

CoreOS层包含其它大多数技术建在其之上的低级别的功能。虽然应用不直接使用这些技术，它们被其它框架使用。在需要显而易见的处理安全或与外设通讯的情形，你也能使用该层提供的框架。

4.1  Core OS包含的框架：
1）Accelerate 加速框架
   Accelerate框架 (Accelerate.framework)包含执行数字信号处理、线性代数、图像处理计算的接口。
   使用该框架的优点是它们针对所有的ios设备上存在的硬件配置做了优化，因此你能写一次代码确保在所有设备上有效运行。
2）Core Bluetooth Framework（核心蓝牙框架）
CoreBluetooth 框架 (CoreBluetooth.framework)允许开发者与蓝牙低耗电外设（LE）交互。
使用该框架的Objective-C接口能够完成如下工作：

           1、扫描蓝牙外设，连接和断开发现的蓝牙外设；

           2、声明应用的服务，转换ios 设备成其它蓝牙设备的外设；

           3、 从IOS设备广播iBeacon信息；

     4、保存你的蓝牙连接的状态，当应用重新启动时恢复那些连接；

          5、蓝牙外设可获得性变化时获得通知。
3）External Accessory Framework（外部附件框架）
       ExternalAccessory 框架(ExternalAccessory.framework)提供与连接到IOS设备的硬件附件通讯的支持。
      附件能通过30-pin连接器或使用蓝牙无线与IOS设备进行连接。该框架给你提供了获得关于每一个可获得的附件信息和启动通讯会话的方式。然后，你可自由的使用附件支持的命令直接操作附件。
4）Generic Security Services Framework（通用安全服务框架）
      GenericSecurity Services 框架 (GSS.framework)给ios应用提供一组标准安全相关的服务。该框架的基本接口规定在IETFRFC2743 andRFC4401。除了提供标准的接口，IOS还包括一些没有在标准中规定但被许多应用需要的一些管理证书需要的额外东西。
5）Security Framework（安全框架）
      除了内建的安全功能，IOS也提供了一个明确的安全框架（Security.framework)，你能用它来保证应用管理的数据的安全。
      该框架提供管理证书、公有和私有key和信任策略的接口。支持产生加密安全伪随机码。它也支持在keychain（保存敏感用户数据的安全仓库）中保存证书和加密key。
      公共加密库提供对称加密、hash认证编码（HMACs）、数字签名等额外支持，数字签名功能本质上与iOS上没有的OpenSSL库兼容。
     在你创建的多个应用之间共享keychain是可能的。共享使它容易在相同的一套应用之间更平滑的协作。例如，你能使用该功能来共享用户口令或其它元素，否则可能使每个应用都需要提示用户。
     为了在应用之间共享数据，必须为每个应用的Xcode工程配置适当的权限。
6）System
      System级包含kernel环境、驱动以及操作系统级别的unix接口。kernel本身负责操作系统的每一个方面：如虚拟内存管理、线程、文件系统、网络和互联通信。在该层的驱动也提供在可获得的硬件与系统框架之间的接口。为了安全，对kernel和驱动的存取被限制到一组有限的系统框架和应用。
    IOS提供一组存取许多操作系统低级别功能的接口。应用通过LibSystem库存取这些功能。该C based的接口提供如下功能的支持：

           1) 多任务（POSIX线程和GCD)

           2) 网络（BSDsockets）

           3) 文件系统存取

           4) 标准I/O

           5) Bonjour和DNS服务

           6)  位置信息

           7)  内存分配

           8) 数学计算
7） 64-Bit Support
      IOS原先是为32-bit架构的设备设计的。自iOS 7，开始支持在64-bit进行编译、链接和调试。所有的系统库和框架是支持64位的，意味着它们能在32-bit和64-bit应用中使用。当以64-bit运行时编译时，应用可能运行的更快，因为在64-bit模式可以获得额外的处理器资源。
      iOS使用OS X和其它64-bitUNIX系统使用的LP64模式，意味着在这些系统移植时不会碰到太头疼的事。
