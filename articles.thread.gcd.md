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
	dispatch_queue_t queue = 	dispatch_queue_create(@"com.example.serialQueue", DISPATCH_QUEUE_SERIAL);
	 
	// 创建并发队列
	dispatch_queue_t barrierQueue	= dispatch_queue_create(@"com.example.concurrentQueue", DISPATCH_QUEUE_CONCURRENT);
	
	// 获取主线程队列
	dispatch_queue_t mainQueue	= dispatch_get_main_queue();
	```
*	可以“man dispatch_object”获取更多信息。


### 4. 基础用法举例

1. 如何创建队列（略）

2. 自定义上下文

```objc
void myFinalizerFunction(void *context)
{
	MyDataContext* theData = (MyDataContext*)context;
	// 清除这个数据的内容
    myCleanUpDataContextFunction(theData);
    // 释放数据.
    free(theData);
}

dispatch_queue_t createMyQueue()
{
    MyDataContext*  data = (MyDataContext*) malloc(sizeof(MyDataContext));
    myInitializeDataContextFunction(data);
    // 创建队列并设置上下文.
    dispatch_queue_t serialQueue =
  dispatch_queue_create("com.example.CriticalTaskQueue", NULL);
    if (serialQueue)
    {
		dispatch_set_context(serialQueue, data);
        dispatch_set_finalizer_f(serialQueue, &myFinalizerFunction); // _f，C函数
	}
	return serialQueue;
}
```

3. 用队列执行、管理任务（略）
4. 执行(block object, function)、完成(block object, funtion)、并发执行循环（略）
5. 挂起、恢复队列（略）（都是简单的api调用）
6. 使用信号量（Dispatch Semaphores）
	*	信号量的作用是控制多个任务对有限数量资源的访问。
	*	当创建信号量（使用dispatch_semaphore_create方法），我们可以指定一个正整数，表示可用资源的数量。
	*	在每一个任务里，调用dispatch_semaphore_wait来等待信号量。
	*	当等待调用返回时，获取资源并做自己的工作。
	*	当我们用到资源后，释放掉它，然后通过调用dispatch_semaphore_signal方法来发出信号。

```objc
// 创建一个信号量
dispatch_semaphore_t fd_sema = dispatch_semaphore_create(getdtablesize() / 2);
// 等待一个空闲的文件描述符
dispatch_semaphore_wait(fd_sema, DISPATCH_TIME_FOREVER);
fd = open("/etc/services", O_RDONLY);
// 当完成时，释放掉文件描述符
close(fd);
dispatch_semaphore_signal(fd_sema);
```

7. Dispatch Group的使用
	*	Dispatch groups是阻塞线程直到一个或多个任务完成的一种方式。(GCD提供了两种通知方式)
	*	dispatch_group_wait。它会阻塞当前线程，直到组里面所有的任务都完成或者等到某个超时发生。
	*	dispatch_group_notify。它以异步的方式工作，当 Dispatch Group中没有任何任务时，它就会执行其代码，那么 completionBlock便会运行。
8. [Operation Queues, Dispatch Queues, Dispatch Sources](https://developer.apple.com/library/ios/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html#//apple_ref/doc/uid/TP40008091-CH102-SW24)
9. Dispatch Queues 和 线程安全
	*	不要在一个正执行在同一个队列的任务中，调用dispatch_sync，会造成死锁(deadlock)。
	*	多线程竞争资源的情况下，避免在dispatch_once中堵塞色（比如：CoreData在global线程中）

### 5. 



