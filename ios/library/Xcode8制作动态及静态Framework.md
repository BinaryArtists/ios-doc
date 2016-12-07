## 使用framework

	* 1. [Xcode 8制作动态及静态Framework](http://blog.csdn.net/u011662987/article/details/53021786)
	* 2. [ios Static Dynamic Library Or Framework](http://qinshiqi.iteye.com/blog/2209131)

### 判断一个Framework支持哪些架构

```
mac-mini:Test Sail$  lipo -info UPModel.framework/UPModel
Non-fat file: UPModel.framework/UPModel is architecture: x86_64
```

### [ iOS 动态 Framework 对App启动时间影响实测](http://blog.csdn.net/hbblzjy/article/details/53464779)

### [ios Static Dynamic Library Or Framework](http://qinshiqi.iteye.com/blog/2209131)

1. Framework 包含什么？

这里来看Alamofire编译后的结果：
```
Alamofire.framework
├── Alamofire
├── Headers
│   ├── Alamofire-Swift.h
│   └── Alamofire.h
├── Info.plist
├── Modules
│   ├── Alamofire.swiftmodule
│   │   ├── arm.swiftdoc
│   │   ├── arm.swiftmodule
│   │   ├── arm64.swiftdoc
│   │   ├── arm64.swiftmodule
│   │   ├── i386.swiftdoc
│   │   ├── i386.swiftmodule
│   │   ├── x86_64.swiftdoc
│   │   └── x86_64.swiftmodule
│   └── module.modulemap
└── _CodeSignature
    └── CodeResources
```

Framework 包括了二进制文件（可动态链接并且为每种处理器架构专属生成），这点和静态库并无区别，但不同的是，它包含其它资源：
头文件 - 也包含Swift symbols所生成的头文件，如 Alamofire-Swift.h 。
所有资源文件的签名 - Framework被嵌入应用前都会被重新签名。
资源文件 - 像图片等文件。
Dynamic Frameworks and Libraries - 参见 Umbrella Frameworks
Clang Module Map 和 Swift modules - 对应处理器架构所编译出的Module文件
Info.plist - 该文件中说明了作者，版本等信息。

Cocoa Touch Framework (实际内容为 Header + 动态链接库 + 资源文件)

2. Static Framework & Dynamic Framework

刚才也说明了Apple所创建的标准 Cocoa Touch Framework 里面包含的是 动态链接库 。而 Dynamic Framework 为 Xcode 6中引入的新特性，仅支持 iOS 8，因为Carthage使用的是该特性，所以仅支持iOS 8，说明上有提。
但新版CocoaPods中使用Framework是能够支持iOS 7的，这说明它不是Dynamic Framework。推断它仅是将Static Library封装入了Framework。还是静态库， 伪Framework 。
关于Static Framework，见：
伪Framework 是指使用Xcode的Bundle来实现的。在使用时和Cocoa Touch Framework没有区别。但通过Framework，可以或者其中包含的资源文件（Image, Plist, Nib）。

	* [SO - Xcode 6 and iOS Static Frameworks](http://stackoverflow.com/questions/25978291/xcode-6-and-ios-static-frameworks)
	* [iOS Universal Framework Mk 8 中文](https://github.com/kstenerud/iOS-Universal-Framework)
	* [iOS-Framework](https://github.com/jverkoey/iOS-Framework)

3. Swift 与 Framework 的关系

在Xcode 6.0 Beta 4的 Release Notes 中，可以找到这句话：
```
Xcode does not support building static libraries that include Swift code. (17181019)
```

如果库是使用Swift语言开发，在build时会得到
``` 	
unknown option character `X' in: -Xlinker
```

CocoaPods 将第三方都编译为Static Library。这导致Pod不支持Swift语言。所以新版Pod已将Static Library改为Framework。
Pods 0.36.0.beta.1 虽然已经支持Swift，但在编译时仍会给出下面警告：
```
ld: warning: embedded dylibs/frameworks only run on iOS 8 or later
```

其它扩展阅读：
	* [How to distribute Swift Library without exposing the source code?](http://stackoverflow.com/questions/25020783/how-to-distribute-swift-library-without-exposing-the-source-code)
	* [Pod Authors Guide to CocoaPods Frameworks](http://blog.cocoapods.org/Pod-Authors-Guide-to-CocoaPods-Frameworks/)

### 库之间的引用

特征：静态库会链接到app binary中，而动态库只是参与链接，在运行时加载
结论：我们在制作静态库是，一般引用第三方静态库、系统动态库；如果你同时引入第三方动态库，那其实在我们要制作的静态库被主工程使用的时候，是无法链接通过的。所以如果需要将第三方动态库封装，那么就要对应制作对应的动态库、iOS 8.0+

详细可以参考：[一篇较为详细的 ios静态动态库 的使用方法总结](http://blog.csdn.net/fallenink/article/details/53501969)
