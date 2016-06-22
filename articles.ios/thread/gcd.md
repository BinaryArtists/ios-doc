## GCD(Grand Central Dispatch)要点

*	[Apple官方参考](https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/)
*	[GCD使用三部曲:基本用法](http://www.jianshu.com/p/d56064507fb8)
*	[GCD入门（一）:基本概念和Dispatch Queue](http://www.dreamingwish.com/article/grand-central-dispatch-basic-1.html)
*	[Grand Central Dispatch In-Depth:Part 1/2](http://www.raywenderlich.com/60749/grand-central-dispatch-in-depth-part-1)
*	[深入理解GCD（－）](http://blog.jobbole.com/66866/)
* [iOS多线程的初步研究（十）-- dispatch同步](http://blog.csdn.net/lengshengren/article/details/12905821)
	> GCD提供两种方式支持dispatch队列同步，即dispatch组和信号量。

### 目录
*	[1. GCD是什么](#1)
*	[2. 优势](#2)
*	[3. GCD对象和ARC](#3)
*	[4. 基础用法举例](#4)
*	[5. 使用GCD途径有几个好处](#5)
*	[6. GCD在并行计算的使用与表现](#6)
*	[7. 管理 Dispatch Sources](#7)
*	[8. 回顾 Dispatch Queue](#8)

<h3 id="1">1. GCD是什么</h3>
*	是libdispatch的市场名称，而libdispatch是Apple的一个库。
*	GCD比之NSOperationQueue更底层更高效，因为它运行在系统级别。
*	除了并行处理能力，GCD还提供高度集成的事件控制系统。可以设置句柄来响应文件描述符、mach ports（Mach port用于OS X上的进程间通讯）、进程、计时器、信号、用户生成事件。这些句柄通过GCD来并发执行。
*	GCD的API，可以使用block，也可以使用传统C机制提供函数指针和上下文指针。
*	在mac的terminal上敲命令“man dispatch”可以获取GCD文档。

<h3 id="2">2. 优势</h3>
*	易用性、效率高、性能优（详细可以看参考链接）

<h3 id="3">3. GCD对象和ARC</h3>
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
*	要主线程去操作UI相关方法：
	```
	实际上Apple规定UIKit的方法都必须放在主线程中调用，事实证明，确实很多不涉及UI操作的UIKit方法都不能在子线程中调用，甚至一些文件加载的函数。而其根本原因是UIKit的这些方法都在内部调用了一些可能产生竞态的资源（很多方法会调用render context来进行中间转换等操作），而一些UIKit方法被验证确实能放在子线程中调用（虽然不推荐这么做，因为这么做不稳定，你不能确定某一天Apple会修改这些方法的内部实现而导致其只能在主线程中执行）也印证了这一点。
	```
*	可以“man dispatch_object”获取更多信息。


<h3 id="4">4. 基础用法举例</h3>

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
		信号量是一个整形值并且具有一个初始计数值，并且支持两个操作：信号通知和等待。当一个信号量被信号通知，其计数会被增加。当一个线程在一个信号量上等待时，线程会被阻塞（如果有必要的话），直至计数器大于零，然后线程会减少这个计数。
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

<h3 id="5">5. 使用GCD途径有几个好处：</h3>
*	平行计算: 注意在第二个版本的代码中， -setSomething:是怎么使用dispatch_async的。调用 -setSomething:会立即返回，然后这一大堆工作会在后台执行。如果updateSomethingCaches是一个很费时费力的任务，且调用者将要进行一项处理器高负荷任务，那么这样做会很棒。
*	安全: 使用GCD，我们就不可能意外写出具有不成对Lock的代码。在常规Lock代码中，我们很可能在解锁之前让代码返回了。使用GCD，队列通常持续运行，你必将归还控制权。
*	控制: 使用GCD我们可以挂起和恢复dispatch queue，而这是基于锁的方法所不能实现的。我们还可以将一个用户队列指向另一个dspatch queue，使得这个用户队列继承那个dispatch queue的属性。使用这种方法，队列的优先级可以被调整——通过将该队列指向一个不同的全局队列，若有必要的话，这个队列甚至可以被用来在主线程上执行代码。
*	集成: GCD的事件系统与dispatch queue相集成。对象需要使用的任何事件或者计时器都可以从该对象的队列中指向，使得这些句柄可以自动在该队列上执行，从而使得句柄可以与对象自动同步。

<h3 id="6">6. GCD在并行计算的使用与表现</h3>
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

<h3 id="7">7. 管理 Dispatch Sources</h3>
*	它监听某类类型事件的对象，当这类事件发生的时候，它自动将一个事件处理句柄，异步提交到指定的队列中进行处理。
*	下面是GCD 10.6.0版本支持的事件：
		1. Mach port send right state changes.
		2. Mach port receive right state changes.
		3. External process state change.
		4. File descriptor ready for read.
		5. File descriptor ready for write.
		6. Filesystem node event.
		7. POSIX signal.
		8. Custom timer.
		9. Custom event.
它支持所有kqueue所支持的事件（kqueue是什么？见http://en.wikipedia.org/wiki/Kqueue）以及mach（mach是什么？见http://en.wikipedia.org/wiki/Mach_(kernel)）端口、内建计时器支持（这样我们就不用使用超时参数来创建自己的计时器）和用户事件。
*	用户事件
	1. 用户事件有两种： DISPATCH_SOURCE_TYPE_DATA_ADD 和 DISPATCH_SOURCE_TYPE_DATA_OR.用户事件源有个 unsigned long data属性，我们将一个 unsigned long传入 dispatch_source_merge_data。当使用 _ADD版本时，事件在联结时会把这些数字相加。当使用 _OR版本时，事件在联结时会把这些数字逻辑与运算。当事件句柄执行时，我们可以使用dispatch_source_get_data函数访问当前值，然后这个值会被重置为0。
	2. 案例：异步更新一个进度条。因为主线程就是GCD的另一个dispatch queue，所以我们可以将GUI更新工作submit到主线程中。然而，这些事件可能会有一大堆，我们不想对GUI进行频繁而累赘的更新，理想的情况是当主线程繁忙时，将所有的改变累积起来。
	```objc
	dispatch_source_t source = dispatch_source_create(DISPATCH_SOURCE_TYPE_DATA_ADD, 0, 0, dispatch_get_main_queue());
dispatch_source_set_event_handler(source, ^{
    [progressIndicator incrementBy:dispatch_source_get_data(source)];
});
dispatch_resume(source);
// processing below
dispatch_apply([array count], globalQueue, ^(size_t index) {
    // do some work on data at index
    dispatch_source_merge_data(source, 1);
});
	```
*	内建事件
	1. 案例：用GCD读取标准输入
	```objc
	dispatch_queue_t globalQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_source_t stdinSource = dispatch_source_create(DISPATCH_SOURCE_TYPE_READ,
                                                       STDIN_FILENO,
                                                       0,
                                                       globalQueue);
dispatch_source_set_event_handler(stdinSource, ^{
    char buf[1024];
    int len = read(STDIN_FILENO, buf, sizeof(buf));
    if(len > 0)
        NSLog(@"Got data from stdin: %.*s", len, buf);
});
dispatch_resume(stdinSource);
	```
	2. 这是标准的UNIX方式来处理事务的好处，不用去写loop。如果使用经典的 read调用，我们还得万分留神，因为返回的数据可能比请求的少，还得忍受无厘头的“errors”，比如 EINTR (系统调用中断)。
	3. 对于其他文件描述符，我们必须考虑在完成读写之后怎样清除描述符。对于dispatch source还处于活跃状态时，我们决不能关闭描述符。如果另一个文件描述符被创建了（可能是另一个线程创建的）并且新的描述符刚好被分配了相同的数字，那么你的dispatch source可能会在不应该的时候突然进入读写状态。de这个bug可不是什么好玩的事儿。
适当的清除方式是使用 dispatch_source_set_cancel_handler，并传入一个block来关闭文件描述符。然后我们使用 dispatch_source_cancel来取消dispatch source，使得句柄被调用，然后文件描述符被关闭。
*	计时器
	1. 计时器事件稍有不同。它们不使用handle/mask参数，计时器事件使用另外一个函数 dispatch_source_set_timer 来配置计时器。
	2. 参数中，数leeway参数比较有意思。这个参数告诉系统我们需要计时器触发的精准程度。所有的计时器都不会保证100%精准，这个参数用来告诉系统你希望系统保证精准的努力程度。如果你希望一个计时器没五秒触发一次，并且越准越好，那么你传递0为参数。另外，如果是一个周期性任务，比如检查email，那么你会希望每十分钟检查一次，但是不用那么精准。所以你可以传入60，告诉系统60秒的误差是可接受的。
这样有什么意义呢？简单来说，就是降低资源消耗。如果系统可以让cpu休息足够长的时间，并在每次醒来的时候执行一个任务集合，而不是不断的醒来睡去以执行任务，那么系统会更高效。如果传入一个比较大的leeway给你的计时器，意味着你允许系统拖延你的计时器来将计时器任务与其他任务联合起来一起执行。

<h3 id="8">8. 回顾 Dispatch Queue</h3>
*	队列的挂起和继续，dispatch_suspend、dispatch_resume
*	dispatch_set_target_queueyou两个作用：
	1. 用来给新建的queue设置优先级
	```objc
	dispatch_queue_t serialQueue = dispatch_queue_create("com.oukavip.www",NULL);  
dispatch_queue_t globalQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND,0);  
dispatch_set_target_queue(serialQueue, globalQueue);  
/* * 第一个参数为要设置优先级的queue,第二个参数是参照物，既将第一个queue的优先级和第二个queue的优先级设置一样。
     */
	```
	2. 修改串行队列的目标队列，使多个串行queue在目标queue上依次挨个执行。
	```objc
	+(void)testTargetQueue {  
    	dispatch_queue_t targetQueue = dispatch_queue_create("test.target.queue", DISPATCH_QUEUE_SERIAL);  
    	//
    	dispatch_queue_t queue1 = dispatch_queue_create("test.1", DISPATCH_QUEUE_SERIAL);
    	//
    	dispatch_set_target_queue(queue1, targetQueue);  
 		//
    	dispatch_async(queue1, ^{  
        	NSLog(@"1 in");  
        	[NSThread sleepForTimeInterval:3.f];  
        	NSLog(@"1 out");  
    	});  
	}
	```
*	单例初始化：dispatch_once。关于这个，遇到一个案例是：
	```objc
	// Thread main....
	-[[SomeThing sharedInstance] doSomeThing];
	// Thread global 23...
	-[[...] doCoreDataOperation]...block
	-[[SomeThing sharedInstance] loadWhatever];
	-[[SomeThing sharedInstance] init];
	/**
	**	23线程卡死了，导致主线程也卡死了
	**	当然，coreData多线程操作，要尤其注意
	**	里面有什么玄机呢？后面来讨论，它的低负载性
	*/
	```
	比 @synchronized方法简单些，并且GCD确保以更快的方式完成这些检测，它保证block中的代码在任何线程通过 dispatch_once 调用之前被执行，但它不会强制每次调用这个函数都让代码进行同步控制。实际上，如果你去看这个函数所在的头文件，你会发现目前它的实现其实是一个宏，进行了内联的初始化测试，这意味着通常情况下，你不用付出函数调用的负载代价，并且会有更少的同步控制负载。

*	连续的数据预取，可以使用串行队列。
*	[dispatch_once的低负载性](http://www.dreamingwish.com/article/gcd-guide-dispatch-once-1.html)
	```
	dispatch_once的低负载性，我们要讨论三种场景：
	第一次执行，block需要被调用，调用结束后需要置标记变量
	非第一次执行，而此时#1尚未完成，线程需要等待#1完成
	非第一次执行，而此时#1已经完成，线程直接跳过block而进行后续任务
 	对于场景#1，整体任务的效率瓶颈完全不在于dispatch_once，而在于block本身占用的cpu时间，并且也只会发生一次。
	对于场景#2，发生的次数并不会很多，甚至很多时候一次都不会发生，假如发生了，那么也只是一个符合预期的行为：后来的线程需要等待第一线程完成。即使你写一个受虐型的单元测试来故意模拟场景#2，也不能说明什么问题，得不到的永远在骚动，被偏爱的都有恃无恐。
	对于场景#3，在程序进行过程中，可能发生成千上万次或者天文数字次，这才是效率提升的关键之处
	```
