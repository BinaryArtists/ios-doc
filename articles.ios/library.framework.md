## 讲讲库：framework、static library、dynamic library

参考链接：
  1. [iOS的主要框架介绍](http://blog.csdn.net/yuhuangc/article/details/7575519)
  2. [iOS库--.a与.framework](http://www.tuicool.com/articles/7J7vai)
  3. [iOS 动态库与静态库的区别（framework,.a,.dylib）](http://www.th7.cn/Program/IOS/201503/405727.shtml)
  4. [如何为iOS项目开发Framework](http://ios.jobbole.com/81583/)

### 目录
* [1. 库](#1)
* [2. 静态库](#2)
* [3. 动态库](#3)
* [4. 框架（framework)](#4)
* [5. 其他](#5)

<h3 id="1">库</h3>

库是共享程序代码的方式，一般分为静态库和动态库。

两个概念：静态链接、动态链接

1. 静态链接

      一般是指在创建应用程序的时候，将库集成进去，这样做的好处就是应用程序包自身可以独立运行，而不好的地方就是包会略显臃肿，库不能共享

2. 动态链接

      创建应用的时候只约定好与库之间的调用关系，而不彻底将库包集成进应用。这样在应用运行时，需要运行环境中提供库，并且连接装载。优劣与静态库相反，动态链接库需要库环境，但由于本身不集成库内容，会比较小，同时也为和其他应用共享库的使用提供了可能。

<h3 id="2">静态库</h3>

1. 静态库：链接时完整地拷贝至可执行文件中，被多次使用就有多份冗余拷贝。
2. iOS里静态库形式：.a和.framework
3. 好处：
    * 模块化，分工合作
    * 避免少量改动经常导致大量的重复编译连接
    * 也可以重用，注意不是共享使用

<h3 id="3">动态库</h3>

1. 动态库：链接时不复制，程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存。
2. iOS里动态库形式：.dylib和.framework
3. 好处：
    * 使用动态库，可以将最终可执行文件体积缩小
    * 使用动态库，多个应用程序共享内存中得同一份库文件，节省资源
    * 使用动态库，可以不重新编译连接可执行程序的前提下，更新动态库文件达到更新应用程序的目的。

    从1可以得出，将整个应用程序分模块，团队合作，进行分工，影响比较小。
    从2可以看出，其实动态库应该叫共享库，那么从这个意义上来说，苹果禁止iOS开发中使用动态库就可以理解了：
    > 因为在现在的iPhone，iPodTouch，iPad上面程序都是单进程的，也就是某一时刻只有一个进程在运行，那么你写个共享库，
    > 共享给谁？（你使用的时候只有你一个应用程序存在，其他的应该被挂起了，即便是可以同时多个进程运行，别人能使用你的共享库里的东西吗？你这个是给你自己的程序定制的。）
    > 目前苹果的AppStore不支持模块更新，无法更新某个单独文件(除非自己写一个更新机制：有自己的服务端放置最新动态库文件)


<h3 id="4">框架（framework)</h3>

1. 是什么？
     框架是一个目录，这个目录包含了共享库，访问共享库里代码的头文件，和其它的图片和声音的资源文件。一个共享库定义的方法或函数可以被应用程序调用。

2. 怎么用？
    * Build Phases -> Link Binary With Libraries 中添加
    * 要使用一个框架，需要将它添加到你的项目中，你的项目才可以使用它。许多应用程序都使用了如Foundation、UIKit、和Core Graphics这些框架。根据你为应用程序选择的模版，相关的框架就已经被自动引入了。如果默认加入的框架不能满足你的应用程序的需求，你也可以加入需要的框架。

3. 系统的.framework是动态库，我们自己建立的.framework是静态库。此时，.framework 可以认为是一个包裹着.a的空壳子。

4. 与上面两者，framework有什么分别呢？
    * .a是一个纯二进制文件，.framework中除了有二进制文件之外还有资源文件。
    * .a文件不能直接使用，至少要有.h文件配合，.framework文件可以直接使用。
    * .a + .h + sourceFile = .framework。
    建议用.framework.

<h3 id="5">其他</h3>

1. [说说iOS中静态库的开发](http://www.molotang.com/articles/1497.html)
--------
所谓SDK（software development kit），无非就是以包的形式提供的一个开发库。

  * 对库的粗浅理解

  无论是操作系统层面的开发，还是上层应用的开发，库这个东西都是少不了的。我没有对库这个概念去找比较精准的定义，不过无论哪个平台，一般应该具有如下特征：
    > 非最终的应用，一般都是给应用提供通用功能服务的，非独立运行的程序集合
    > 一般都是编译过的，方便连接使用

  * 苹果的库
  当我们打开Xcode，创建应用，打开工程文件中的Build Phases，里面的Link binary with Libraries为我们提供了各个方面的系统库，有的以.dylib的形式提供，有的以Framework的方式给出。

  Framework实际上就是苹果平台特有的对二进制库的一种包装方式，除了代码编译后的包，还有头文件等内容。关于具体的介绍，可参见官方文档，其中给出了Framework的文件包结构等描述：í
  https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPFrameworks/Frameworks.html

  从连接方式上来看，动态连接和静态连接在Mac OSX上都对开发者开放，而在iOS上目前已知的只支持静态方式的连接库开发，没有找到可以支持动态库的官方说明，动态库只能使用系统提供的。

  * iOS中库的开发

  由于移动设备软硬件资源有限，苹果对iOS上库的开发有了严格限制。

  在iOS平台上，打开Xcode，选择工程或者Target类型，默认情况下我们可以看到的只有Cocoa Touch Static Library。这个实际上就是我们前面提到的静态链接库，编译出来的Product就是一个.a形式的程序包。

  在静态的.a库使用时至少需要注意以下两点：

  product仅仅是一个.a包，使用时需要另外配合.h头文件
  静态的.a库是会根据工程和target配置，使用特定的处理器架构指令集编译打包的，一般不会同时支持iOS设备的arm指令集和模拟器的i386
  由于.a的方式是Xcode默认支持的，所以使用也很多。但从发布和使用方便的方面来看，Framework的方式则是更优的选择。而Xcode本身并没有提供iOS平台上的工程模板支持，第三方的一个比较优秀的工具就是iOS Universal Framework。

  * iOS Universal Framework

  这是非苹果官方的一个制作Framework库的第三方工具，目前已经出了第8个版本(mk8)。这个工具提供了Fake Framework和Real Framework两类，其中后者需要执行一个脚本安装，会在Xcode的默认配置上做一些改动，此后工程和Target模板中就会多一个Framework的选择。Real Framework的开发和引入使用都需要执行shell脚本安装iOS Universal Framework，但这些通常来说都不是问题，而且由于Real Framework是”Real的Framework”，避免掉了Fake Framework的一些问题，更多的被接受使用。

  如上面所述，iOS Universal Framework解决了如下问题，方便发布和使用：

  会根据可见级别配置，将必要的头文件自动添加到Framework中，开发给应用者使用
  既然叫“Universal”，自然是将真机的arm指令集和模拟器的i386整合到了一起，一个包打出来，方便在各个平台上调试和使用
  还提供了embedded framework方式，可以把nib、图片等资源文件一起集成
  打开Xcode中使用iOS Universal Framework的库工程，其Build Phases中会有一个或者几个Run Script，这实际上就是这个工具在Build过程中所做的事情。

  其中在老版本(mk7)中，前前后后使用了3个shell脚本，而在最新的mk8中，只在最后执行了一个python的脚本，感兴趣的可以看看其中的内容。
  https://github.com/kstenerud/iOS-Universal-Framework

  再稍微说一句，就是最新的mk8版本，打包的时候需要配好architecture，并且用Archive来打包，否则Build出来的包依然是处理器架构指令集支持不全的。

  比如，我的配置是:
  Architectures: armv7, armv7s
  Valid Architectures: armv7, armv7s, i386

  * Umbrella库和符号冲突
  如果你已经看过上面苹果官方介绍Framework的文档链接内容，你应该会看到其中提到了一个Umbrella Framework的概念。这里我顺便造了一个词“Umbrella库”。其实大概的意思就是两个库存在包含关系，进而形成了库的包含层次关系。

  其实这种库的封包形式或者依赖关系在其他开发平台也常见，比如在Java开发中使用Maven管理依赖关系。但这种包含会带来冲突问题。比如Maven中有不同版本但却同名的包，这时Maven提供的是一种仲裁机制，提示使用哪个版本的。

  那么在没有命名空间概念，只靠前缀来进行类区分的iOS平台开发中，出现这种冲突的概率似乎显得更大。一个常见的问题就是“duplicate symbol”。

  这个问题并不是一定有包含关系的.a或者.framework造成的，如果在构建一个.a的源码中使用了一个第三方的源码一起编译，而在使用.a的工程中或者另一个.a中也用到了这套源码，那么duplicate symbol也是必然的。

  由于.a和.framework的构建中也可以集成引入其他的.a库，那么使得这种duplicate symbol错误的可能出现场景变得更多，而且解决起来更为麻烦。

  所以，库开发比较有经验的“燕子姐姐”给了一个规则，就是构建framework的时候，只要所需要的头文件能找到就好，使.a库不要再link的libraries中出现。

  * 最后稍微说说处理器架构相关
  iOS设备目前都是采用ARM(Advanced RISC Machine)指令集架构的处理器，但不同代的产品对应不同的版本。

  大概一个对应列表如下：
  armv6：iPhone 2G/3G，iPod 1G/2G
  armv7：iPhone 3GS/4/4s，iPod 3G/4G，iPad 1G/2G/3G
  armv7s：iPhone5
  arm64: iPhone5s
  我们用Xcode5默认的是支持armv7和armv7s，支持arm64的iPhone5s是兼容和可以使用这个配置打包出来的应用的。

  如果想把不同指令集的.a包合并起来，可以使用lipo工具。

--------
