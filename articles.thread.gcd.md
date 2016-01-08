## GCD(Grand Central Dispatch)要点
*	[Apple官方参考](https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/)
*	[GCD使用三部曲:基本用法](http://www.jianshu.com/p/d56064507fb8)
*	[GCD入门（一）:基本概念和Dispatch Queue](http://www.dreamingwish.com/article/grand-central-dispatch-basic-1.html)

### 1. GCD是什么
*	是libdispatch的市场名称，而libdispatch是Apple的一个库。
*	GCD比之NSOperationQueue更底层更高效，因为它运行在系统级别。
*	除了并行处理能力，GCD还提供高度集成的事件控制系统。可以设置句柄来响应文件描述符、mach ports（Mach port用于OS X上的进程间通讯）、进程、计时器、信号、用户生成事件。这些句柄通过GCD来并发执行。
*	GCD的API，可以使用block，也可以使用传统C机制提供函数指针和上下文指针。
*	在mac的terminal上敲命令“man dispatch”可以获取GCD文档。

### 2. 优势
*	易用性、效率高、性能优（详细可以看参考链接）

### 3. GCD对象和ARC
*	［基础概念］串行（Serial）、并发（Concurrent）、并行（Parallelism）、异步（Synchronous）、同步（Asynchronous）
*	［基础概念］上下文切换（Context Switch），一个上下文切换指当你在单个进程里切换执行不同的线程时存储与恢复执行状态的过程。
*	［GCD对象（Dispatch Objects）］，详见[Apple官方参考]中的Data types，如dispatch_queue_t(_t是为结构体，struct，命名的一种规范)。
*	［内存管理］NO ARC下注意使用dispatch_retain、dispatch_release，区别于CoreFoundation的retain、release方法。
*	［分发队列（Dispatch Queues）］，强大的执行多任务的工具，先进先出队列（FIFO），不需要使用者创建线程，方便实用。
*	［队列类型（Queue Types）］，有三种队列类型：Serial，串行队列；Concurrent，在iOS5之前，并发队列一般指的就是全局队列(Global queue)，进程中存在四个全局队列：高、中(默认)、低、后台四个优先级队列，可以调用dispatch_get_global_queue函数传入优先级来访问队列。iOS5之后，可以用dispatch_queue_create。Main dispatch queue，主线程，它能保证所有的任务都在主线程执行，而主线程是唯一可用于更新UI的线程。

	```objc
	// 创建串行队列
	dispatch_queue_t queue = 	dispatch_queue_create(queueName, DISPATCH_QUEUE_SERIAL);
	 
	// 创建并发队列
	dispatch_queue_t barrierQueue	= dispatch_queue_create(name, DISPATCH_QUEUE_CONCURRENT);
	
	// 获取主线程队列
	dispatch_queue_t mainQueue	= dispatch_get_main_queue();
	```
*	可以“man dispatch_object”获取更多信息。


### 4. 基础用法举例


### 5. 



