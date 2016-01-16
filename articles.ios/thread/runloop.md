## Runloop要点

### 参考链接
[RunLoop个人小结](http://www.jianshu.com/p/37ab0397fec7)

### 目录
*	[1. 前言](#1)
*	[2. runloop关键类型](#2)
*	[3. 理解runloop内部原理](#3)
*	[4. runloop使用点](#4)
*	[5. 从事件处理看runloop](#5)
*	[6. runloop应用场合](#6)

<h3 id="1">前言</h3>
来自“简书”：关于Runloop的资料，官方文档总是太过简练，网上资源说法也不太统一，只能从CFRunLoopRef开源代码着手，试着学习总结下。（NSRunloop是对CFRunloopRef的面向对象封装，但是不是线程安全）。

<h3 id="2">runloop类型梳理</h3>

*	CFRunLoopRef
	a. 创建与退出：mode切换和item依赖
	
	> 主线程的runloop自动创建，子线程的runloop默认不创建（在子线程中调用NSRunLoop *runloop = [NSRunLoop currentRunLoop];
获取RunLoop对象的时候，就会创建RunLoop）

	> runloop退出的条件：app退出；线程关闭；设置最大时间到期；modeItem为空；

	> 同一时间一个runloop只能在一个mode，切换mode只能退出runloop，再重进指定mode（隔离modeItems使之互不干扰）

	> 一个item可以加到不同mode；一个mode被标记到commonModes里（这样runloop不用切换mode）。
	
	b. 启动runloop
	
*	CFRunLoopSourceRef

*	CFRunLoopObserverRef

*	CFRunLoopTimerRef
	
*	是什么？
*	.....
*	.....

<h3 id="3">理解runloop内部原理</h3>
*	简述
		
*	与线程的关系

		Runloop的寄生于线程：一个线程只能有唯一对应的runloop；但这个根runloop里可以嵌套子runloops；
		自动释放池寄生于Runloop：程序启动后，主线程注册了两个Observer监听runloop的进出与睡觉。一个最高优先级OB监测Entry状态；一个最低优先级OB监听BeforeWaiting状态和Exit状态。
		线程(创建)-->runloop将进入-->最高优先级OB创建释放池-->runloop将睡-->最低优先级OB销毁旧池创建新池-->runloop将退出-->最低优先级OB销毁新池-->线程(销毁)
		
*	自运作
	![图1](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/runloop_sources.jpg)
		
		CFRunLoop对象监听输入源(如图1)，当它们ready的时候，分发控制去处理。
		
		输入源可能是：
		*	用户输入设备
		*	网络连接
		*	周期性或延时性事件
		*	异步回调
		
		后面三种类型（），可以被runloop监听

<h3 id="4">runloop使用点（如何使用）</h3>
*	管理源
*	管理定时器
*	管理端口
*	调度和取消消息

<h3 id="5">从事件处理看runloop（何时使用）</h3>
*	界面刷新
*	事件响应
*	手势识别
*	GCD任务
*	timer
*	网络请求

<h3 id="6">runloop具体应用场合</h3>
*	滑动和图片刷新
*	常驻子线程，保持一直处理事件
