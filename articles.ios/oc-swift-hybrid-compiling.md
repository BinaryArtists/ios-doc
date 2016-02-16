## Objective-C与swift混编

来源于：
  1. [Swift与Objective-C和Cocoa混合编程的基本设置](http://c.biancheng.net/cpp/html/2269.html)
  2. [Swift与Objective-C的交互](http://c.biancheng.net/cpp/html/2291.html)
  3. [使用Objective-C编写Swift类](http://c.biancheng.net/cpp/html/2266.html)
  4. [在Swift中使用Cocoa数据类型](http://c.biancheng.net/cpp/html/2452.html)
  5. [采用Cocoa设计模式](http://c.biancheng.net/cpp/html/2267.html)
  6. [与C语言交互编程](http://c.biancheng.net/cpp/html/2282.html)
  7. [在一个工程中同时使用Swift和Objective-C](http://c.biancheng.net/cpp/html/2268.html)
  8. [将Objective-c代码迁移到Swift](http://c.biancheng.net/cpp/html/2289.html)

参考链接：
  0. [官方文档](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/index.html#//apple_ref/doc/uid/TP40014216-CH2-ID0)
  1. [在项目里交叉使用Swift和OC](http://blog.csdn.net/huangchentao/article/details/35278663)
  2. [初探swift语言的学习笔记九(OC与Swift混编)](http://blog.csdn.net/fengsh998/article/details/34440159)

例子工程：[SwiftProject](https://github.com/BinaryArtists/Musician)（测试代码，主要看SwiftProjectTests target中）

### 目录
  * [1. 源文件](#1)
    * [1.1 OC调用swift](#1.1)
      * 1.1.1 类继承
      * 1.1.2 类型兼容
    * [1.2 swift调用OC](#1.2)
  * [2. framework](#2)
  * [3. 静态库](#3)
  * [4. 动态库](#4)
  * [5. 转载](#5)

<h3 id="1.1">OC调用swift</h3>

> 观察对应当前，oc文件的target
> targets->build settings->Apple LLVM 7.0-language-Modules->Enable Modules (C and Objective-c)->Yes
> targets->build settings->Product Module Name中设置的名字（这个名称很重要，swift 的头文件就是根据这个来命名的。）
> 在oc文件中，引用头文件：{ProductModuleName}-Swift.h

***注意***
凡是用Swift写的类，如果不继成自NSObject或NSObject 的派生类，哪么编译后将不会生成对应的转换类。从而使得OC 中找不到相应的声明。

1. 类继承
  * 报错：cannot subclass a class with objc_subclassing_restricted attribute
  * [Prevent class from being subclassed in Objective-c](http://stackoverflow.com/questions/19194807/prevent-class-from-being-subclassed-in-objective-c)
    ```objc
    #if defined(__has_attribute) && __has_attribute(objc_subclassing_restricted)
    # define FOO_FINAL __attribute__((objc_subclassing_restricted))
    #else
    # define FOO_FINAL
    #endif

    FOO_FINAL
    @interface Foo : NSObject
    @end

    @interface Bar : Foo
    @end
    ```
  * 所以我们在{ProjectName}-Swift.h文件里可以找到答案

2. oc使用swift中的协议

[在swift中的写法是：](http://www.jianshu.com/p/9876b2eb8fa2)
```objc
  @objc protocol MYProtocol {
    func MyFunc( )
  }
```

在oc中写法一样

2. 类型兼容问题（类继承本质是，类型兼容问题？）
String vs NSString
Double vs NSNumber


<h3 id="1.2">swift调用OC</h3>

> 创建oc源码文件的时候，可以顺着编译器提示，生成一个头文件<productname-bridging-header.h>
> 确认在target->build settings->Swift Compiler-Code Generation->Objective-C Bridging Header中设置了该桥接的头文件（当然该头文件可以自己创建，任意命名，并手动设置）
> 想要在swift类中调用的OC头文件，在上述桥接头文件中，一一import即可。

***注意***
Xcode会自动将OC的方法转换成Swift中的使用方式,方法名一致。

1. 类继承
  * swift中，可以继承oc类

2.

<h3 id="2">framework</h3>

1. [swift与OC混编高级教程之混编框架的创建和调用](http://blog.sina.com.cn/s/blog_877e9c3c0101sexl.html)

该文章都解决问题的思路，依然是 第一部分中 讲到的混编中的引用问题。

2. 尝试集成环信 by fa



<h3 id="3">静态库</h3>
<h3 id="4">动态库</h3>

### 以上三者要解决的，其实也是语言层面的问题。如有问题，即时反馈哦。

<h3 id="5">转载</h3>

1. [iOS 里面 Swift与Objective-C混编，Swift与C++混编的一些比较](http://www.tuicool.com/articles/QZNrErM)

  * Swift和Objective-C

我们都体会得到Swift调用Objective-C并不难，也不麻烦。你只要在swift的工程里创建OC的文件，系统就会提问帮你生成一个bridging-header，只要在这个bridging-header里import所有要用到的OC的头文件，就可以在swift文件里用swift的语言风格来调用OC类了。

  * Swift和C++

C++ 并不 是 像 Objective-C 这样。首先，我们应该想到 Objective-C 与 C++ 是 可以 混编 得 perfect ，而且可以使用 STL ，办法就 是 将 OC 的.m文件后缀改成.mm， 然后在 OC 类中写一个 wraper ，然后在里面调用 C++ 的类，封装成几个公共的方法就可以了。这时再用 swift 来调用 wraper 里的方法，就可以执行 C++ 语句了。

2. [Objective-C 与 Swift 混编之路](http://blog.csdn.net/mmoaay/article/details/48422309)

结合框架的混编

 V      {View-Swift}{SnapKit-Swift}{Masonry-OC}
 ________________________________________________

 C      {ViewController-swift}{Manager-swift}     业务层
 ________________________________________________

 M      {Form-swift/oc}{Model-swift/oc}
 ________________________________________________

  {CFRuntime-swift} {MJExtension-oc}              解析层
 ________________________________________________

  {Alamofire-swift} {AFNetworking-oc}             网络层
 ________________________________________________

***踩坑时间***

* OC 中的复杂宏
```objc
#define RGBCOLOR(R,G,B) .....
```

Swift 编译器不包含预处理器。取而代之的是，它充分利用了编译时属性，生成配置，和语言特性来完成相同的功能。所以对于上述类似的宏定义，建议用方法重新封装一次使用。

* OC 中的宏和 Swift 中的类同名

因为 Swift 不能使用 #define，而 OC 可以，所以你可能会在 OC 中定义一个和 Swift 中类同名字的宏，如果你从来没有在 OC 中使用 Swift 代码提供的功能（也就是从来没有 #import XXX-Swift.h），编译时不会有任何问题，但是如果一旦使用了，就会报如下的错误：

"Expected ';' at end of declaration list."

这里是因为我使用 #define MView (@"MView") 将 MView 定义成了 NSString 类型的宏，而在 Swift 中 MView 是 UIView 的子类，在 #import "MDemo-Swift.h" 之后， MView 就被重复定义了，从而导致错误。

> 更新：如果 OC 中的类和 Swift 中的类同名，也符合上述情况。

* Swift 使用 OC 中的枚举

> 如果只是单纯使用值，可以直接使用枚举
> 如果需要对枚举值进行运算，则需要使用 .value

* OC 使用 Swift 中的枚举

> 需要在 Swift 的枚举定义前加 @objc 修饰符
> 枚举的类型必须是 Int

* @objc / dynamic / NS*   [Swift与Objective-C的兼容“黑魔法”：@objc和Dynamic](http://www.csdn.net/article/2015-02-04/2823831-swift-objc-and-dynamic)

> 如果 Swift 类需要在 OC 中使用，建议继承 NS* 系列的类
> 如果 Swift 类中的成员或者方法需要在 OC 中使用，使用 @objc 修饰符
> 如果 Swift 类中的成员需要支持 KVC/KVO，使用 dynamic 修饰符

* IBOutlet vs IBOutletCollection

Swift 中没有 IBOutletCollection ，而是如下的方式实现 IBOutletCollection：

```swift
  @IBOutlet var labels: [UILabel]!
```

这个 IBOutlet 在 xib/Storyboard 中的情况如下：

***请看原文***

需要注意的是：和 OC 的 IBOutletCollection 不同，labels 中的元素不一定是按照 black，white，blue，green 的顺序排列！

* 重复包含

OC 中可能会碰到 A 类头文件需要包含 B 类头文件，B 类头文件同时也需要包含 A 类头文件的情况，这个时候用 @class 即可解决问题。但是当 OC 中的 A 类头文件需要包含 XXX-Swift.h，而 XXX-Bridging-Header.h 中又 import A类头文件，这个时候就比较尴尬了。看下面的例子：

```swift
import Cocoa

class MManager: NSObject {
  var mData: MData?

  func getSomethingWithTarget:(target:ViewController!) {
    target.mData = self.mData
  }
}
```

XXX-Bridging-Header.h
```
#import "ViewController.h"
```

```
#import <UIKit/UIKit.h>
#import "MDemo-Swift.h"

@interface ViewController : UIViewController
@property (nonatomic, strong) MData *mData;
@end
```

上面例子的情况是这样的：

首先，我们有两个 Swift 类 MManager 和 MData。 一个 OC 类 ViewController 。

然后，MManager 类中的一个方法需要传入 ViewController 类实例的句柄，然后把一个 MData 类实例赋值给 ViewController 类实例中的成员变量 mData（这个 mData 的类型是 MData ）。

在这种场景下，就会出现重复包含，因为已经有了 MManager 类中的一个方法需要传入 ViewController 类实例的句柄 这样一个条件，所以我们必须在 XXX-Bridging-Header.h 中 #import "ViewController.h"，这样我们就只能通过在 ViewController.m 文件中去 #import "XXX-Swift.h" 的方式来避免重复包含。此时我们需要将成员变量 mData 定义为 id 类型，当在ViewController.m 文件中具体用的时候再将其强制转换为 MData 类型。如下：

```
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController
@property (nonatomic, strong) id mData;
@end
```

```
#import "ViewController.h"
#import "Hehe-Swift.h"

@interface ViewController ()
@end
@implementation ViewController
- (void)viewDidLoad {
  [super viewDidLoad];

  if ([self.mData isKindOfClass:[MData class]]) {
    //
  }
}
@end
```

即可解决重复包含的问题。

这样的处理方式存在的问题是成员变量 mData 可以接受任何类型，所以我们在使用的时候要判断一下 mData 是否是我们需要的数据类型，这里我们介绍一下怎么判断 Swift 的 AnyObject 和 OC 的 id 是什么类型：

OC：
```
if ([self.mData isKindOfClass:[MData class]]) { // 如果 self.mData 不是 MData 类型，判断不成功
}
```

Swift：
```
if let data = self.mData as? MData { // 如果 self.mData 不是 MData 类型，判断不成功
}
```

* properties

如果 OC 的类导入到 Swift 中使用，类的 properties 会有如下变化：

Swift 中的属性都是 noatomic 的，所以 OC 类中的 atomic 将会失效
OC 类中重写的 getter/setter 方法都将失效

***Hello！ Swift 2***

* var -> let

相比 Swift 1.2，Swift 2 强烈要求将在本方法体中值不会改变的量声明为常量，否则会出现 warning，所以在编写 Swift 1.2 的代码时，可以提前注意这一点，从而减小转换成本。

* println

Swift 2 中这个方法被删除，不要使用。

* do/while -> repeat/while

因为有变化，所以建议用 for / for…in 代替

* 面向协议编程

从 Swift 2 开始，我们可以对协议进行扩展，从此正式开启了 Swift 的面向协议编程时代。因为目前还没有在项目中具体使用，所以研究不是很深，大家可以参考下面三篇译文：

[Swift 2：面向协议编程](http://www.swiftyper.com/Swift/introducing-protocol-oriented-programming-in-swift-2.html)

[如何正确使用协议](https://github.com/kevin833752/MyTranslationSet/blob/master/TranslationSet/协议%20-%20我当前的推荐%28Protocols%20-%20My%20Current%20Recommendations%29.md)

[Swift 2.0 中的面向协议的MVVM](http://swift.gg/2015/09/11/swift-2-0-protocol-oriented-mvvm/)
