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
*	［Dispatch Objects］GCD对象，详见[Apple官方参考]中的Data types，如dispatch_queue_t(_t是为结构体，struct，命名的一种规范)。
*	NO ARC下注意使用dispatch_retain、dispatch_release，区别于CoreFoundation的retain、release方法。
*	［Serial & Concurrent & Parallelism]
*	[Synchronous & Asynchronous］串行、并发。（？？？参照另一个文档，未写好）。
*	
*	可以“man dispatch_object”获取更多信息。


### 



