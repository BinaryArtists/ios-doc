## iOS静态库 【.a 和framework】【超详细】

[iOS静态库 【.a 和framework】【超详细】](https://my.oschina.net/kaqijiang/blog/649632)
[framework--怎么样使用bundle来共享资源](http://www.jianshu.com/p/ff50bce87f55)

### 什么是库？

库是共享程序代码的方式。

库从本质上来说是一种可执行代码的二进制格式，可以被载入内存中执行。库分静态库和动态库两种。
iOS中的静态库有 .a 和 .framework两种形式；动态库有.dylib 和 .framework 形式，后来.dylib动态库又被苹果替换成.tbd的形式。

### 静态库与动态库的区别？

静态库： 链接时完整地拷贝至可执行文件中，被多次使用就有多份冗余拷贝。

动态库： 链接时不复制，程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存。[ios暂时只允许使用系统动态库]；

静态库和动态库是相对编译期和运行期的：静态库在程序编译时会被链接到目标代码中，程序运行时将不再需要改静态库；而动态库在程序编译时并不会被链接到目标代码中，只是在程序运行时才被载入，因为在程序运行期间还需要动态库的存在。
总结：同一个静态库在不同程序中使用时，每一个程序中都得导入一次，打包时也被打包进去，形成一个程序。而动态库在不同程序中，打包时并没有被打包进去，只在程序运行使用时，才链接载入（如系统的框架如UIKit、Foundation等），所以程序体积会小很多，但是苹果不让使用自己的动态库，否则审核就无法通过。

### iOS里静态库形式？

.a和.framework

### iOS里动态库形式？

.dylib和.framework

### framework为什么既是静态库又是动态库？

系统的.framework是动态库，我们自己建立的.framework是静态库。

### a与.framework有什么区别？

.a是一个纯二进制文件，.framework中除了有二进制文件之外还有资源文件。

.a文件不能直接使用，至少要有.h文件配合，.framework文件可以直接使用。

.a + .h + sourceFile = .framework。

建议用.framework.

### 为什么要使用静态库？

方便共享代码，便于合理使用。

实现iOS程序的模块化。可以把固定的业务模块化成静态库。

和别人分享你的代码库，但不想让别人看到你代码的实现。

开发第三方sdk的需要。

### 制作静态库时的几点注意：

1注意理解：无论是.a静态库还.framework静态库，我们需要的都是二进制文件+.h+其它资源文件的形式，不同的是，.a本身就是二进制文件，需要我们自己配上.h和其它文件才能使用，而.framework本身已经包含了.h和其它文件，可以直接使用。

2图片资源的处理：两种静态库，一般都是把图片文件单独的放在一个.bundle文件中，一般.bundle的名字和.a或.framework的名字相同。.bundle文件很好弄，新建一个文件夹，把它改名为.bundle就可以了，右键，显示包内容可以向其中添加图片资源。

3category是我们实际开发项目中经常用到的，把category打成静态库是没有问题的，但是在用这个静态库的工程中，调用category中的方法时会有找不到该方法的运行时错误（selector not recognized），解决办法是：在使用静态库的工程中配置other linker flags的值为-ObjC。

4如果一个静态库很复杂，需要暴露的.h比较多的话，就可以在静态库的内部创建一个.h文件（一般这个.h文件的名字和静态库的名字相同），然后把所有需要暴露出来的.h文件都集中放在这个.h文件中，而那些原本需要暴露的.h都不需要再暴露了，只需要把.h暴露出来就可以了。

### 其他注意

[IOS静态库导入的framework编译入.a文件中](http://www.cocoachina.com/bbs/read.php?tid-231964.html)

framework 的第三方库，只是参与link，并不会 build 进你自己新建的 .a的binary，（*对这句话的理解就是，.a的库build过程是没有链接的，所以如果.a的proj中引入第三方framework，是无法build进去的，所以建议建立framework的静态库*）

所以，你的的项目必须同时引用 你自己的.a 和 这个.a引用的所有的地方 framework(.frameworkj结尾的)

同理，可以可以控制引用的第三方.a文件不被打入新的.a，只要在静态库项目的binary里删除.a引用，但是是searchpath里保留，一样可以编译通过，这样可以避免耦合导致的多  framework冲突，特别是公用一些第三方基础库，比如 sdwebimage之类的

[ Xcode6.4打包静态framework时包含其他第三方framework(如Alipay),而且里面写了OC的分类,然后出现了问题](http://www.cocoachina.com/bbs/read.php?tid-323584-page-3.html)

[Xcode 8制作动态及静态Framework](http://blog.csdn.net/u011662987/article/details/53021786)

我们制作动态库的时候，选的设备是模拟器，如果选真机的话，那生成的库也只能在真机上使用，那我们该怎样制作一个通用的动态库呢? 简单的方法是分别生成模拟器和真机上运行的库，然后在合并，这个方法，在每次生成动态库的时候，过程都会很繁琐，下面我们用一个脚本来自动完成它。

[Xcode6.4打包静态framework时包含其他第三方framework(如Alipay),而且里面写了OC的分类,然后出现了问题](http://www.cocoachina.com/bbs/read.php?tid-323584.html)

请在Build Setting中检索Other Linker flags 加入参数 -ObjC

在写SDK的时候需要注意的是如果不想让接入方在接入的时候加入 -ObjC参数，就不要使用catagory。

```
-ObjC

这个flag告诉链接器把库中定义的Objective-C类和Category都加载进来。这样编译之后的app会变大（因为加载了其他的objc代码进来）。但是如果静态库中有类和category的话只有加入这个flag才行。

-all_load

这个flag是专门处理-ObjC的一个bug的。用了-ObjC以后，如果类库中只有category没有类的时候这些category还是加载不进来。变通方法就是加入-all_load或者-force-load。-all_load会强制链接器把目标文件都加载进来，即使没有objc代码。-force_load在xcode3.2后可用。但是-force_load后面必须跟一个只想静态库的路径。

As of Xcode 4.2, you only need to go to the application that is linking against the library (not the library itself) and click the project in the Project Navigator, click your app's target, then build settings, then search for "Other Linker Flags", click the + button, and add '-ObjC'. '-all_load' and '-force_load'
```

上面这个问题的solution推荐看这一篇文章：[iOS中静态库(static library, .a文件)中的category变得可用](http://blog.csdn.net/skylin19840101/article/details/51821932)
