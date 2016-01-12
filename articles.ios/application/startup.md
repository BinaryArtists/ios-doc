## 应用的启动与运转原理讲解（系统都做了什么）

### 参考链接
1. [iOS程序启动与运转](http://www.jianshu.com/p/37ab0397fec7)
2. []()

### 目录
[1. 预加载](#1)
[2. ](#2)
[3. ](#3)
[4. ](#4)
[5. ](#5)
[6. ](#6)
[7. ](#7)


### 预加载
*	加载framework
*	加载动态链接库
*	加载必要的资源：启动图片、info.list
*	？？？加载二进制
*	检查沙盒
*	Objective-C Class load initialize
*	_attribute_((constructor))函数，C++全局对象构造函数
*	
*	还有什么？？？？？？？？？？？？？？？？？？？？？？

### main.m
*	main()
*	UIApplicationMain()
	- 初始化UIApplication单例对象
  	- 初始化AppDelegate对象，并设为UIApplication对象的代理
  	- 检查Info.plist设置的xib文件是否有效，如果有则解冻Nib文件并设置outlets，创建显示key window、rootViewController、与rootViewController关联的根view（没有关联则看rootViewController同名的xib），否则launch之后由程序员手动加载。
  	- 建立一个主事件循环，其中包含UIApplication的Runloop来开始处理事件
  	
### AppDelegate.m
*	UIApplication
	1. 通过window管理视图；
	2. 发送Runloop封装好的control消息给target；
	3. 处理URL，应用图标警告，联网状态，状态栏，远程事件等。
*	AppDelegate：
	1. 管理UIApplication生命周期和应用的五种状态(notRunning/inactive/active/background/suspend)。
*	Key Window
	1. 显示view；
	2. 管理rootViewcontroller生命周期；
	3. 发送UIApplication传来的事件消息给view。
*	rootViewController：
	1. 管理view（view生命周期；view的数据源/代理；view与superView之间事件响应nextResponder的“备胎”）;
	2. 界面跳转与传值；
	3. 状态栏，屏幕旋转。
*	view：
	1. 通过作为CALayer的代理，管理layer的渲染（顺序大概是先更新约束，再layout再display）和动画（默认layer的属性可动画，view默认禁止，在UIView的block分类方法里才打开动画）。layer是RGBA纹理，通过和mask位图（含alpha属性）关联将合成后的layer纹理填充在像素点内，GPU每1/60秒将计算出的纹理display在像素点中。
	2. 布局子控件（屏幕旋转或者子视图布局变动时，view会重新布局）。
	3. 事件响应：event和guesture。
*	runloop:
	1. (要让马儿跑）通过do-while死循环让程序持续运行：接收用户输入，调度处理事件时间。
	2. (要让马儿少吃草）通过mach_msg()让runloop没事时进入trap状态，节省CPU资源。




  
  

