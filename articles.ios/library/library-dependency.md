## [转载][iOS应用所依赖的系统库检查]

在iOS开发中，如果需要做数据收集时，标识设备曾经和现在可用的办法，其中提到了广告Id，即advertisingIdentifier的使用和“滥用”情况下被苹果审核Reject的情况。

那么苹果是用什么方式发现应用中使用到了哪些系统库，调用了其中的方法，使用了库中的类及对象的属性了的呢？这里就简要向大家介绍一个工具otool。

0. 简要介绍

按照文档的介绍，otool就是针对目标文件的展示工具（object file displaying tool）。

使用otool可以对指定的目标文件(object file)或库文件中的特定部分以特定的形式展现出来。

otool能够理解Mach-O格式和任何通用文件格式。

1. 依赖的库查询

在iOS开发中，通常我们Xcode工程的一个最终的Product是app格式，而如果你在Finder中点击“显示包内容”看看app里都有什么，你会看到里面的好多东西，有nib、plist、mobileprovision和各种图片资源等，而其中最重要的一个文件就是被标为“Unix可执行文件”(用file查看是Mach-O executable arm)类型的文件。我们的代码逻辑都在这里面。

比如这个可执行文件名为ex，我们通过使用:

    otool -L ex

就能看到这个应用运行所需要的系统库，显示结果的格式通常如下：

    /usr/lib/libicucore.A.dylib (compatibility version 1.0.0, current version 51.1.0)
  	/usr/lib/libstdc++.6.dylib (compatibility version 7.0.0, current version 100.0.0)
  	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 221.0.0)
  	/System/Library/Frameworks/AdSupport.framework/AdSupport (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/Accelerate.framework/Accelerate (compatibility version 1.0.0, current version 4.0.0)
  	/System/Library/Frameworks/OpenGLES.framework/OpenGLES (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/Accounts.framework/Accounts (compatibility version 1.0.0, current version 113.0.0)
  	/System/Library/Frameworks/Social.framework/Social (compatibility version 1.0.0, current version 87.0.0)
  	/System/Library/Frameworks/MapKit.framework/MapKit (compatibility version 1.0.0, current version 14.0.0)
  	/System/Library/Frameworks/ImageIO.framework/ImageIO (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/AssetsLibrary.framework/AssetsLibrary (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/Security.framework/Security (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/MobileCoreServices.framework/MobileCoreServices (compatibility version 1.0.0, current version 51.0.0)
  	/System/Library/Frameworks/CoreVideo.framework/CoreVideo (compatibility version 1.2.0, current version 1.8.0)
  	/System/Library/Frameworks/MessageUI.framework/MessageUI (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/CoreLocation.framework/CoreLocation (compatibility version 1.0.0, current version 1613.5.2)
  	/System/Library/Frameworks/SystemConfiguration.framework/SystemConfiguration (compatibility version 1.0.0, current version 615.0.0)
  	/System/Library/Frameworks/CoreData.framework/CoreData (compatibility version 1.0.0, current version 479.2.0)
  	/System/Library/Frameworks/CoreTelephony.framework/CoreTelephony (compatibility version 1.0.0, current version 1645.0.0)
  	/System/Library/Frameworks/CoreText.framework/CoreText (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/MediaPlayer.framework/MediaPlayer (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/CoreMedia.framework/CoreMedia (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/AudioToolbox.framework/AudioToolbox (compatibility version 1.0.0, current version 492.0.0)
  	/System/Library/Frameworks/CFNetwork.framework/CFNetwork (compatibility version 1.0.0, current version 672.0.8)
  	/System/Library/Frameworks/QuartzCore.framework/QuartzCore (compatibility version 1.2.0, current version 1.8.0)
  	/System/Library/Frameworks/AddressBookUI.framework/AddressBookUI (compatibility version 1.0.0, current version 33.0.0)
  	/System/Library/Frameworks/AddressBook.framework/AddressBook (compatibility version 1.0.0, current version 30.0.0)
  	/System/Library/Frameworks/AVFoundation.framework/AVFoundation (compatibility version 1.0.0, current version 2.0.0)
  	/System/Library/Frameworks/UIKit.framework/UIKit (compatibility version 1.0.0, current version 2903.23.0)
  	/System/Library/Frameworks/Foundation.framework/Foundation (compatibility version 300.0.0, current version 1047.22.0)
  	/System/Library/Frameworks/CoreGraphics.framework/CoreGraphics (compatibility version 64.0.0, current version 600.0.0)
  	/usr/lib/libsqlite3.dylib (compatibility version 9.0.0, current version 158.0.0)
  	/usr/lib/libz.1.dylib (compatibility version 1.0.0, current version 1.2.5)
  	/usr/lib/libobjc.A.dylib (compatibility version 1.0.0, current version 228.0.0)
  	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1198.0.0)
  	/System/Library/Frameworks/CoreFoundation.framework/CoreFoundation (compatibility version 150.0.0, current version 847.21.0)
这样，这个应用依赖于哪些系统库，版本号分别是多少就一目了然了。

2. 汇编码

其实，查看依赖系统库仅仅是其中的一小功能。otool命令配上不同的参数可以发挥很强大的功用。

如果使用

    otool -tV ex

则整个ARM的汇编码就都显示出来了。能看到ARM的汇编码，那接下来怎么用就看大家的了，想象空间无限啊！

3. Mach-O头结构等

其实otool文档本身的介绍就是：otool是对目标文件或者库文件的特定部分进行展示。这里举个例子，看一下其头部的内容是怎样的：

    otool -h ex

结果显示如下：

    Mach header

    magic cputype cpusubtype  caps    filetype ncmds sizeofcmds      flags

    0xfeedface      12          9  0×00          2    51       5608 0×00218085

至于各字段的含义，可参看/usr/include/mach-o/loader.h。

4. 结语

实际上，这篇文章所介绍的otool仅仅是个小用，使用otool我们可以得到目标文件中的所有信息，所以说otool是个非常强大的工具，甚至优于nm，如果和ar、lipo等工具结合使用，威力无穷！感兴趣的朋友可以琢磨下。
