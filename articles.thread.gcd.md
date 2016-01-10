## GCD(Grand Central Dispatch)要点
*	[Apple官方参考](https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/)
*	[GCD使用三部曲:基本用法](http://www.jianshu.com/p/d56064507fb8)
*	[GCD入门（一）:基本概念和Dispatch Queue](http://www.dreamingwish.com/article/grand-central-dispatch-basic-1.html)
*	[Grand Central Dispatch In-Depth:Part 1/2](http://www.raywenderlich.com/60749/grand-central-dispatch-in-depth-part-1)
*	[深入理解GCD（－）](http://blog.jobbole.com/66866/)

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
*	［基础概念］临界区（Critical Section），这段代码不能被并发执行，共享资源被竞争会不可预测的结果。
*	［基础概念］竟态条件（Race Condition），指基于特定序列或时机的事件的软件系统以不受控制的方式运行的行为
*	［基础概念］死锁（Deadlock），线程安全（Thread Safe）
*	［GCD对象（Dispatch Objects）］，详见[Apple官方参考]中的Data types，如dispatch_queue_t(_t是为结构体，struct，命名的一种规范)。
*	［内存管理］NO ARC下注意使用dispatch_retain、dispatch_release，区别于CoreFoundation的retain、release方法。而，ARC自动管理GCD是从iOS6开始的。
*	［分发队列（Dispatch Queues）］，强大的执行多任务的工具，先进先出队列（FIFO），不需要使用者创建线程，方便实用。
*	［队列类型（Queue Types）］，有三种队列类型：Serial，串行队列；Concurrent，在iOS5之前，并发队列一般指的就是全局队列(Global queue)，进程中存在四个全局队列：高、中(默认)、低、后台四个优先级队列，可以调用dispatch_get_global_queue函数传入优先级来访问队列。iOS5之后，可以用dispatch_queue_create。Main dispatch queue，主线程，它能保证所有的任务都在主线程执行，而主线程是唯一可用于更新UI的线程。

	```objc
	// 创建串行队列
	// Apple建议我们使用倒置域名来命名队列，比如 “com.dreamingwish.subsystem.task”。
	dispatch_queue_t queue = dispatch_queue_create(@"com.example.serialQueue", DISPATCH_QUEUE_SERIAL);
	// 创建并发队列
	dispatch_queue_t barrierQueue = dispatch_queue_create(@"com.example.concurrentQueue", DISPATCH_QUEUE_CONCURRENT);
	// 获取主线程队列
	dispatch_queue_t mainQueue = dispatch_get_main_queue();
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
```objc
// === now on main thread
dispatch_sync(dispatch_get_main_queue(), ^{
    //
	NSLog(@"dead or not ?");
});
// 死锁；如果在非主线程这么做，是可以同步取值的。
```
	*	多线程竞争资源的情况下，避免在dispatch_once中堵塞色（比如：[CoreData的多线程问题](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/library/coredata.multithread_issues.md)）

10. 用串行队列（Serial）替代锁（Lock），来完成同步机制
	*	传统多线程编程中，常遇到的这样的场景：对一个资源，同时进行写入或读出的操作；冲突的解决方式是，用锁来保护该资源对象。（例子copy的，看看就好，不必与使用场景对号入座，看明白原理就行。）
	```objc
	// ...
	NSLock *lock;
	-(id)something {
    	id localSomething;
    	[lock lock];
    	localSomething = [[something retain] autorelease];
    	[lock unlock];
    	return localSomething;
	}
	-(void)setSomething:(id)newSomething {
    	[lock lock];
    	if(newSomething != something)
    	{
        	[something release];
        	something = [newSomething retain];
        	[self updateSomethingCaches];
    	}
    	[lock unlock];
	}
	```
	*	使用GCD，可以使用queue来替代：
	```objc
	// ...
	dispatch_queue_t queue;
	-(id)something {
		__block id localSomething;
		dispatch_sync(queue, ^{
			localSomething = [something retain];
		});
		return [localSomething autorelease];
	}
	-(void)setSomething:(id)newSomething {
		dispatch_async(queue, ^{
			if (newSomething != something) {
				[something release];
				something = [newSomething retain];
				[self updateSomethingCaches];
			}
		});
	}
	```

### 5. 使用GCD途径有几个好处：
*	平行计算: 注意在第二个版本的代码中， -setSomething:是怎么使用dispatch_async的。调用 -setSomething:会立即返回，然后这一大堆工作会在后台执行。如果updateSomethingCaches是一个很费时费力的任务，且调用者将要进行一项处理器高负荷任务，那么这样做会很棒。
*	安全: 使用GCD，我们就不可能意外写出具有不成对Lock的代码。在常规Lock代码中，我们很可能在解锁之前让代码返回了。使用GCD，队列通常持续运行，你必将归还控制权。
*	控制: 使用GCD我们可以挂起和恢复dispatch queue，而这是基于锁的方法所不能实现的。我们还可以将一个用户队列指向另一个dspatch queue，使得这个用户队列继承那个dispatch queue的属性。使用这种方法，队列的优先级可以被调整——通过将该队列指向一个不同的全局队列，若有必要的话，这个队列甚至可以被用来在主线程上执行代码。
*	集成: GCD的事件系统与dispatch queue相集成。对象需要使用的任何事件或者计时器都可以从该对象的队列中指向，使得这些句柄可以自动在该队列上执行，从而使得句柄可以与对象自动同步。

### 6. GCD在并行计算的使用与表现
*	dispatch_async\dispatch_group(dispatch_group_async, dispatch_group_notify)\dispatch_apply
*	dispatch_group的[一个例子](http://blog.csdn.net/q199109106q/article/details/8566300)：
```objc
// 根据url获取UIImage  
-(UIImage *)imageWithURLString:(NSString *)urlString {  
    NSURL *url = [NSURL URLWithString:urlString];  
    NSData *data = [NSData dataWithContentsOfURL:url];  
    // 这里并没有自动释放UIImage对象  
    return [[UIImage alloc] initWithData:data];  
}  
-(void)downloadImages {  
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    // 异步下载图片  
    dispatch_async(queue, ^{  
        // 创建一个组  
        dispatch_group_t group = dispatch_group_create();  
        __block UIImage *image1 = nil;  
        __block UIImage *image2 = nil;  
        // 关联一个任务到group  
        dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{  
            // 下载第一张图片  
            NSString *url1 = @"http://car0.autoimg.cn/upload/spec/9579/u_20120110174805627264.jpg";  
            image1 = [self imageWithURLString:url1];  
        });  
        // 关联一个任务到group  
        dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{  
            // 下载第一张图片  
            NSString *url2 = @"http://hiphotos.baidu.com/lvpics/pic/item/3a86813d1fa41768bba16746.jpg";  
            image2 = [self imageWithURLString:url2];  
        });
        // 等待组中的任务执行完毕,回到主线程执行block回调  
        dispatch_group_notify(group, dispatch_get_main_queue(), ^{  
            self.imageView1.image = image1;
            self.imageView2.image = image2;
            // 千万不要在异步线程中自动释放UIImage，因为当异步线程结束，异步线程的自动释放池也会被销毁，那么UIImage也会被销毁
            // 在这里释放图片资源  
            [image1 release];  
            [image2 release];  
        });
        // 释放group  
        dispatch_release(group);  
    });  
}
```
*	使用 Barriers，它可以让你在并发队列中，使用同步机制。当遭遇barrier，那个并发队列延迟执行barrier block，直到在它之前提交的block都执行完毕；barrier就像将一个并发队列，变成一个串行队列，而它自己就是那个断点；断点完成之后，并发队列恢复原有执行状态，继续并发执行。
```objc
// 不阻塞当前线程。
void dispatch_barrier_async( dispatch_queue_t queue, dispatch_block_t block);
// 阻塞当前线程。直到barrier执行完毕。
void dispatch_barrier_sync( dispatch_queue_t queue, dispatch_block_t block);
```

```
		说到底，GCD就是系统来帮用户来管理线程，而不需要再编写线程代码。程序员只需要专心编写执行某项功能的代码，添加到block或方法（函数）中，然后可以有下面两种方式来处理block或方法（函数）。
        1.直接将block加入到dispatch queues
        2.将Dispatch source封装为一个特定类型的系统事件，当系统事件发生时提交一个特定的block对象或函数到dispatch queue
```

### 7. 管理 Dispatch Sources
*	


### 8. 

