## XCode issures

### [Xcode的Architectures、Valid Architectures和Build Active Architecture Only属性](http://www.tuicool.com/articles/aeiaUr)

  * Architectures
    这代表，在这个项目里你想要Xcode编译的目标设备列表。

  * Valid Architectures
    默认为 Standard architectures(armv7,armv7s,arm64)

    原因解释如下：
    使用 standard architectures (including 64-bit)(armv7,armv7s,arm64) 参数，则打的包里面有32位、64位两份代码，在iPhone5s（ iPhone5s的cpu是64位的 ）下，会首选运行64位代码包，其余的iPhone（ 其余iPhone都是32位的,iPhone5c也是32位 ）， 只能运行32位包，但是包含两种架构的代码包，只有运行在ios6，ios7系统上。这也就是说，这种打包方式，对手机几乎没啥要求，但是对系统有要求，即ios6以上。而使用 standard architectures (armv7,armv7s) 参数， 则打的包里只有32位代码， iPhone5s的cpu是64位，但是可以兼容32位代码，即可以运行32位代码。但是这会降低iPhone5s的性能。 其余的iPhone对32位代码包更没问题， 而32位代码包，对系统也几乎也没什么限制。

    所以总结如下：  
    要发挥iPhone5s的64位性能，就要包含64位包，那么系统最低要求为ios6。 如果要兼容ios5以及更低的系统，只能打32位的包，系统都能通用，但是会丧失iPhone5s的性能。

  * Build Active Architecture Only
    这个属性设置为yes，是为了debug的时候编译速度更快，它只编译当前的architecture版本。
    而设置为no时，会编译所有的版本。
    这个是设备对应的architecture：
    armv6：iPhone 2G/3G，iPod 1G/2G
    armv7：iPhone 3GS/4/4s，iPod 3G/4G，iPad 1G/2G/3G
    armv7s：iPhone5, iPod5
    arm64：iPhone5s，ipad air,ipad mini2
    编译出的版本是向下兼容的，比如你设置此值为yes，用iphone4编译出来的是armv7版本的，iphone5也可以运行，但是armv6的设备就不能运行。所以，一般debug的时候可以选择设置为yes，release的时候要改为no，以适应不同设备。

### XCode Architecture与IPA包大小关系

1. Build Active Architecture Only（Valid Architectures 略）

     （设备对应的architecture，详细看 2.）属性设置为yes，是为了debug的时候编译速度更快，它只编译当前的architecture版本（属性设置yes、XCode成功连接调试设备，才奏效）。而设置为no时，会编译所有的版本。
     编译出的版本是向下兼容的，比如你设置此值为yes，用iphone4编译出来的是armv7版本的，iphone5也可以运行，但是armv6的设备就不能运行。所以，一般debug的时候可以选择设置为yes，release的时候要改为no，以适应不同设备。

2. XCode里的3个重要参数：

———————————————————————————————————————–
【Architecture】项目可支持的指令集
【Valid architectures】项目编译时的指令集，一般与【Architecture】保持一致，特殊情况见下3
【Build Active Architecture Only】是否只编译基于当前构架的指令集
———————————————————————————————————————–

a、设置A

优点：IPA包在各个构架上运行最流畅最高效；
缺点：IPA包里需要包含各个构架的指令集（armv6/armv7/armv7s/arm64）体积过大，可能会比设置B多8M左右大小。。。

具体设置方法：
【Valid architectures】= 例如：armv6 armv7 armv7s arm64，因此在不同构架上运行时，会优先调用当前构架的指令集，使代码运行优化高效。
【Build Active Architecture Only】= No

b、设置B
优点：如果想IPA包体积最小，则IPA包里只需要包含能运行的最低构架的指令集即可（高版本指令集会向下兼容低版本）；
缺点：在高版本构架上代码可能不是最优化，效率有可能会降低。

具体设置方法：
【Valid architectures】= 项目可以正常运行的最低指令集，现在一般填写armv7即可，即：可以在iPhone 4/4S及以后的机型上运行。
【Build Active Architecture Only】= Yes/No，任意均可。

c、如果项目需要在模拟器上调试，同时项目里有第三方库，可以在真机运行，但是模拟器编译却失败，可能需要增加对i386指令集的支持：

具体设置方法：
【Valid architectures】 = 增加对i386指令集的支持即可，例如：armv7 i386

### 头文件搜索

Search Paths中有
Header Search Paths：添加系统头文件搜索目录
User Header Search Paths：添加用户头文件搜索目录

### [ios build时，Undefined symbols for architecture xxx](http://www.th7.cn/Program/IOS/201410/296636.shtml)

这2天升级到xcode6，用ios8 SDK编译老项目，各种Undefined symbols for architecture xxx，精神差点崩溃了。不过最后还是解决了，本文简单总结一下
简单来说，Undefined symbols基本上等于JAVA的ClassNotFoundException，最常见的原因有这几种：
build的时候没有加framework
比如说，有一段代码我用了OpenGL，引入了头文件
#import <OpenGLES/ES2/glext.h>
build的时候，compile阶段没有问题，但是link就报错Undefined symbols for architecture xxx（这里xxx可能是armv7s，armv7或者arm64，取决于配置，稍后会说）。解决方法是在Build Phases的Link Binary With Libraries里加入OpenGLES.framework，再编译就ok了
工程依赖的库，编译时Architectures不匹配
在Build Settings里，第一项Architectures，是配置项目的编译体系结构，主要有下面3个配置项：
Architectures：将要创建的Bundle支持的ARCH
Valid Architectures：有效的ARCH，这个配置项没什么用，一般配置成armv7，armv7s，arm64就行了
Build Active Architecture Only：是否只打当前连接设备对应的arch
在真机上，常见的ARCH有3个：armv7，armv7s，arm64
armv7：对应iPhone4和iPhone4S
armv7s：对应iPhone5和iPhone5C，还有早期的iPad
arm64/armv8：对应iPhone5S和iPhone6系列，以及比较新的iPad，如iPad mini2，iPad Air
而ARCH是向下兼容的，比如用armv7打出来的包，可以运行在arm64架构的设备上；反之不行。所以如果应用要支持iPhone4系列，Architectures就一定要包含armv7才行
而Build Active Architecture Only是指是否仅当前连接的设备的架构打包。比如Architectures配置了armv7和arm64，Build Active Architecture Only设置为YES，那么连接iPhone4的时候，就会以armv7打包；连接iPhone5S的时候，就会以arm64打包。如果Build Active Architecture Only设置为NO，那么就会2种架构都打，在运行期根据实际的设备架构来执行。所以最后打出来的Bundle体积会比较大
说了这么多，这个为什么造成Undefined symbols呢？因为还有另外一条规则，就是build link阶段，用arm64生成的.o文件，无法link用armv7s或者armv7生成的.o文件，所以就会link error
常见的情况是，项目引用了一个第三方库（比如从pod来的库），而这个第三方库打包的时候只支持armv7s和armv7，而项目有使用arm64打包，这个时候就会由于无法link，而报错Undefined symbols
解决的办法是，或者重新打包第三方库，加入arm64；或者自己的项目去掉arm64
有时候在模拟器上无法构建，在真机上可以
这种情况我只遇到过一次。我们的app可以连接一个外厂商的蓝牙打印机，对方提供了一个lib。当我们的项目引入了这个lib之后，就无法在模拟器上build通过了，但是在真机上是没问题的

### 
