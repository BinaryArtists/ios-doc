## Runloop要点

### 参考链接
1. [RunLoop个人小结](http://www.jianshu.com/p/37ab0397fec7)
2. [深入理解runloop](http://blog.ibireme.com/2015/05/18/runloop/)
3. [ iOS多线程开发（三）－－－Run Loop（二，三）](http://blog.chinaunix.net/uid-24862988-id-3411917.html)

### 目录
*	[1. 前言](#1)
*	[2. runloop关键类型](#2)
*	[3. 理解runloop内部原理](#3)
*	[4. 苹果用 RunLoop 实现的功能](#4)
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
	
	```objc
	// 用DefaultMode启动
	void CFRunLoopRun(void) {
    CFRunLoopRunSpecific(CFRunLoopGetCurrent(), kCFRunLoopDefaultMode, 1.0e10, false);
	}
	// 用指定的Mode启动，允许设置RunLoop最大时间（假无限循环），执行完毕是否退出
	int CFRunLoopRunInMode(CFStringRef modeName, CFTimeInterval seconds, Boolean stopAfterHandle) {
    return CFRunLoopRunSpecific(CFRunLoopGetCurrent(), modeName, seconds, returnAfterSourceHandled);
	}
	```
	
	c. CFRunLoopModeRef：runloop自动创建对应的mode；mode只能添加不能删除
	
	```objc
	// 添加mode
	CFRunLoopAddCommonMode(CFRunLoopRef runloop, CFStringRef modeName);
	//
	// 1. kCFRunLoopDefaultMode: 默认 mode，通常主线程在这个 Mode 下运行。
	// 2. UITrackingRunLoopMode: 追踪mode，保证Scrollview滑动顺畅不受其他 mode 影响。
	// 3. UIInitializationRunLoopMode: 启动程序后的过渡mode，启动完成后就不再使用。
	// 4. GSEventReceiveRunLoopMode: Graphic相关事件的mode，通常用不到。
	// 5. kCFRunLoopCommonModes: 占位mode，作为标记DefaultMode和CommonMode用。
	```
	
*	CFRunLoopSourceRef：事件来源
	来源呢？(也就是使用场合)
	
	```objc
	1) Port-Based Sources:与内核端口相关(CFRunLoopMachPerformCallBack)
		or Custom Input Sources:与自定义source相关
	2) 使用线程定时器
	3) Cocoa Perform Selector Sources:与PerformSEL方法相关）
	4) 使线程周期性工作
	```

	CFRunLoopSource数据结构（source0/source1, 见[官方文档](https://developer.apple.com/library/ios/documentation/CoreFoundation/Reference/CFRunLoopRef/index.html)）:
	```objc
	// source0 (manual): order(优先级)，callout(回调函数)
	CFRunLoopSource {
		order =..., 
		{
			callout =... 
		}
	}
	//
	// source1 (mach port)：order(优先级)，port:(端口), callout(回调函数)
	CFRunLoopSource {
		order = ..., 
		{	port = ..., 
			callout =...
		}
	}
	```

	source0：event事件，只含有回调，需要标记待处理（signal），然后手动将runloop唤醒（wakeup）；
	source1 ：包含一个 mach_port 和一个回调，被用于通过内核和其他线程发送的消息，能主动唤醒runloop。

*	CFRunLoopObserverRef：监听runloop状态，接收回调信息（常见于自动释放池创建销毁）

	数据结构:
	```objc
	// Observer：order(优先级)，ativity(监听状态)，callout(回调函数)
	CFRunLoopObserver {
		order = ...,
		activities = ..., 
		callout = ...
	}
	```

	创建与添加:
	```objc
	// 第一个参数用于分配该observer对象的内存空间
	// 第二个参数用以设置该observer监听什么状态
	// 第三个参数用于标识该observer是在第一次进入run loop时执行还是每次进入run loop处理时均执行
	// 第四个参数用于设置该observer的优先级,一般为0
	// 第五个参数用于设置该observer的回调函数
	// 第六个参数observer的运行状态   
	CFRunLoopObserverCreateWithHandler(CFAllocatorGetDefault(), kCFRunLoopAllActivities, YES, 0, ^(CFRunLoopObserverRef observer, CFRunLoopActivity activity) {
      // 执行代码
}
	```
	
	监听的状态:
	```objc
	typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
    	kCFRunLoopEntry         = (1UL << 0), // 即将进入Loop
    	kCFRunLoopBeforeTimers  = (1UL << 1), // 即将处理 Timer
    	kCFRunLoopBeforeSources = (1UL << 2), // 即将处理 Source
    	kCFRunLoopBeforeWaiting = (1UL << 5), // 即将进入休眠
    	kCFRunLoopAfterWaiting  = (1UL << 6), // 刚从休眠中唤醒
    	kCFRunLoopExit          = (1UL << 7), // 即将退出Loop
	};
	```

*	CFRunLoopTimerRef：系统内“定时闹钟”

	NSTimer和performSEL方法实际上是对CFRunloopTimerRef的封装；runloop启动时设置的最大超时时间实际上是GCD的dispatch_source_t类型。

	数据结构:
	```objc
	// Timer：interval:(闹钟间隔), tolerance:(延期时间容忍度)，callout(回调函数)
	CFRunLoopTimer {
		firing =..., 
		interval = ...,
		tolerance = ...,
		next fire date = ...,
		callout = ...
	}
	```

	创建与生效:
	```objc
	// NSTimer：
    // 创建一个定时器（需要手动加到runloop的mode中）
	 + (NSTimer *)timerWithTimeInterval:(NSTimeInterval)ti invocation:(NSInvocation *)invocation repeats:(BOOL)yesOrNo;
	//
    // 默认已经添加到主线程的runLoop的DefaultMode中 
	+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti invocation:(NSInvocation *)invocation repeats:(BOOL)yesOrNo;
	```
	
	```objc
	// performSEL方法
	// 内部会创建一个Timer到当前线程的runloop中（如果当前线程没runloop则方法无效；performSelector:onThread: 方法放到指定线程runloop中）
	-(void)performSelector:(SEL)aSelector withObject:(id)anArgument afterDelay:(NSTimeInterval)delay
	```

	相关类型（GCD的timer与CADisplayLink)

	GCD的timer：
	```objc
	dispatch_source_t 类型，可以精确的参数，不用以来runloop和mode，性能消耗更小。
	//
	dispatch_source_set_timer(dispatch_source_t source, // 定时器对象
                              dispatch_time_t start, // 定时器开始执行的时间
                              uint64_t interval, // 定时器的间隔时间
                              uint64_t leeway // 定时器的精度
                              );
    ```

	CADisplayLink ：
	
		Timer的tolerance表示最大延期时间，如果因为阻塞错过了这个时间精度，这个时间点的回调也会跳过去，不会延后执行。
		CADisplayLink 是一个和屏幕刷新率一致的定时器，如果在两次屏幕刷新之间执行了一个长任务，那其中就会有一帧被跳过去（和 NSTimer 相似，只是没有tolerance容忍时间），造成界面卡顿的感觉。

<h3 id="3">理解runloop内部原理</h3>
*	简述
		
*	与线程的关系

		Runloop的寄生于线程：一个线程只能有唯一对应的runloop；但这个根runloop里可以嵌套子runloops；
		自动释放池寄生于Runloop：程序启动后，主线程注册了两个Observer监听runloop的进出与睡觉。一个最高优先级OB监测Entry状态；一个最低优先级OB监听BeforeWaiting状态和Exit状态。
		线程(创建)-->runloop将进入-->最高优先级OB创建释放池-->runloop将睡-->最低优先级OB销毁旧池创建新池-->runloop将退出-->最低优先级OB销毁新池-->线程(销毁)
		
*	自运作
	Run loop从两个不同的事件源中接收消息。Input sources(CFRunLoopSource)投递异步消息，通常来自于另一个thread或另一个应用程序。Timer sources(CFRunLoopTimer)当在计划的时间或重复的时间间隔内投递同步消息。两种事件源都使用应用程序指定的处理方式对到达的事件进行处理。下图展示了run loop和不同的事件源结构。

	![图1](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/runloop_sources.jpg)
		
		CFRunLoop对象监听输入源(如图1)，当它们ready的时候，分发控制去处理。
		
		输入源可能是：
		*	用户输入设备
		*	网络连接
		*	周期性或延时性事件
		*	异步回调
		
		后面三种类型（），可以被runloop监听

*	代码实现

	```objc
	// RunLoop的实现
	int CFRunLoopRunSpecific(runloop, modeName, seconds, stopAfterHandle) {
    // 0.1 根据modeName找到对应mode
    CFRunLoopModeRef currentMode = __CFRunLoopFindMode(runloop, modeName, false);
    // 0.2 如果mode里没有source/timer/observer, 直接返回。
    if (__CFRunLoopModeIsEmpty(currentMode)) return;
    // 1.1 通知 Observers: RunLoop 即将进入 loop。---（OB会创建释放池）
    __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopEntry);
    // 1.2 内部函数，进入loop
    __CFRunLoopRun(runloop, currentMode, seconds, returnAfterSourceHandled) {
        Boolean sourceHandledThisLoop = NO;
        int retVal = 0;
        do {
            // 2.1 通知 Observers: RunLoop 即将触发 Timer 回调。
            __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeTimers);
            // 2.2 通知 Observers: RunLoop 即将触发 Source0 (非port) 回调。
            __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeSources);
            // 执行被加入的block
            __CFRunLoopDoBlocks(runloop, currentMode);
            // 2.3 RunLoop 触发 Source0 (非port) 回调。
            sourceHandledThisLoop = __CFRunLoopDoSources0(runloop, currentMode, stopAfterHandle);
            // 执行被加入的block
            __CFRunLoopDoBlocks(runloop, currentMode);
            // 2.4 如果有 Source1 (基于port) 处于 ready 状态，直接处理这个 Source1 然后跳转去处理消息。
            if (__Source0DidDispatchPortLastTime) {
                Boolean hasMsg = __CFRunLoopServiceMachPort(dispatchPort, &msg)
                if (hasMsg) goto handle_msg;
            }
            // 3.1 如果没有待处理消息，通知 Observers: RunLoop 的线程即将进入休眠(sleep)。--- (OB会销毁释放池并建立新释放池)
            if (!sourceHandledThisLoop) {
                __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeWaiting);
            }
            // 3.2. 调用 mach_msg 等待接受 mach_port 的消息。线程将进入休眠, 直到被下面某一个事件唤醒。
            // -  一个基于 port 的Source1 的事件。
            // -  一个 Timer 到时间了
            // -  RunLoop 启动时设置的最大超时时间到了
            // -  被手动唤醒
            __CFRunLoopServiceMachPort(waitSet, &msg, sizeof(msg_buffer), &livePort) {
                mach_msg(msg, MACH_RCV_MSG, port); // thread wait for receive msg
            }
            // 3.3. 被唤醒，通知 Observers: RunLoop 的线程刚刚被唤醒了。
            __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopAfterWaiting);
            // 4.0 处理消息。
            handle_msg:
            // 4.1 如果消息是Timer类型，触发这个Timer的回调。
            if (msg_is_timer) {
                __CFRunLoopDoTimers(runloop, currentMode, mach_absolute_time())
            } 
            // 4.2 如果消息是dispatch到main_queue的block，执行block。
            else if (msg_is_dispatch) {
            __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__(msg);
            } 
            // 4.3 如果消息是Source1类型，处理这个事件
            else {
                CFRunLoopSourceRef source1 = __CFRunLoopModeFindSourceForMachPort(runloop, currentMode, livePort);
                sourceHandledThisLoop = __CFRunLoopDoSource1(runloop, currentMode, source1, msg);
                if (sourceHandledThisLoop) {
                    mach_msg(reply, MACH_SEND_MSG, reply);
                }
            }
            // 执行加入到Loop的block
            __CFRunLoopDoBlocks(runloop, currentMode);
            // 5.1 如果处理事件完毕，启动Runloop时设置参数为一次性执行,设置while参数退出Runloop
            if (sourceHandledThisLoop && stopAfterHandle) {
                retVal = kCFRunLoopRunHandledSource;
            // 5.2 如果启动Runloop时设置的最大运转时间到期，设置while参数退出Runloop
            } else if (timeout) {
                retVal = kCFRunLoopRunTimedOut;
            // 5.3 如果启动Runloop被外部调用强制停止，设置while参数退出Runloop
            } else if (__CFRunLoopIsStopped(runloop)) {
                retVal = kCFRunLoopRunStopped;
            // 5.4 如果启动Runloop的modeItems为空，设置while参数退出Runloop
            } else if (__CFRunLoopModeIsEmpty(runloop, currentMode)) {
                retVal = kCFRunLoopRunFinished;
            }
            // 5.5 如果没超时，mode里没空，loop也没被停止，那继续loop，回到第2步循环。
        } while (retVal == 0);
    }
    // 6. 如果第6步判断后loop退出，通知 Observers: RunLoop 退出。--- (OB会销毁新释放池)
    __CFRunLoopDoObservers(rl, currentMode, kCFRunLoopExit);
	}
	```

*	Runloop状态大致分为：

	```
	1. Entry：通知OB（创建pool）；
	2. 执行阶段：按顺序通知OB并执行timer，source0；若有source1执行source1；
	3. 休眠阶段：利用mach_msg判断进入休眠，通知OB（pool的销毁重建）；被消息唤醒通知OB；
	4. 执行阶段：按消息类型处理事件；
	5. 判断退出条件：如果符合退出条件（一次性执行，超时，强制停止，modeItem为空）则退出，否则回到第2阶段；
	6. Exit：通知OB（销毁pool）。
	```
	
*	Runloop本质：mach port和mach_msg()

		Mach是XNU的内核，进程、线程和虚拟内存等对象通过端口发消息进行通信，		Runloop通过mach_msg()函数发送消息，如果没有port 消息，内核会将线程置于		等待状态 mach_msg_trap() 。如果有消息，判断消息类型处理事件，并通过		modeItem的callback回调(处理事件的具体执行是在DoBlock里还是在回调里目前		我还不太明白？？？)。
		Runloop有两个关键判断点，一个是通过msg决定Runloop是否等待，一个是通过判		断退出条件来决定Runloop是否循环。

<h3 id="4">runloop使用点（如何使用）</h3>
*	管理源
*	管理定时器
*	管理端口
*	调度和取消消息

<h3 id="5">苹果用 RunLoop 实现的功能</h3>
*	界面刷新
*	
	当UI改变（ Frame变化、 UIView/CALayer 的继承结构变化等）时，或手动调用了 UIView/CALayer 的 setNeedsLayout/setNeedsDisplay方法后，这个 UIView/CALayer 就被标记为待处理。
	苹果注册了一个用来监听BeforeWaiting和Exit的Observer，在它的回调函数里会遍历所有待处理的 UIView/CAlayer 以执行实际的绘制和调整，并更新 UI 界面。
	
*	事件响应

	当一个硬件事件(触摸/锁屏/摇晃/加速等)发生后，首先由 IOKit.framework 生成一个 IOHIDEvent 事件并由 SpringBoard 接收， 随后由mach port 转发给需要的App进程。
苹果注册了一个 Source1 (基于 mach port 的) 来接收系统事件，通过回调函数触发Sourece0（所以UIEvent实际上是基于Source0的），调用 	_UIApplicationHandleEventQueue() 进行应用内部的分发。
_UIApplicationHandleEventQueue() 会把 IOHIDEvent 处理并包装成 UIEvent 进行处理或分发，其中包括识别 UIGesture/处理屏幕旋转/发送给 UIWindow 等。

*	手势识别

	如果上一步的 _UIApplicationHandleEventQueue() 识别到是一个guesture手势，会调用Cancel方法将当前的touchesBegin/Move/End 系列回调打断。随后系统将对应的 UIGestureRecognizer 标记为待处理。
	苹果注册了一个 Observer 监测 BeforeWaiting (Loop即将进入休眠) 事件，其回调函数为 _UIGestureRecognizerUpdateObserver()，其内部会获取所有刚被标记为待处理的 GestureRecognizer，并执行GestureRecognizer的回调。
	当有 UIGestureRecognizer 的变化(创建/销毁/状态改变)时，这个回调都会进行相应处理。

*	GCD任务

	当调用 dispatch_async(dispatch_get_main_queue(), block) 时，libDispatch 会向主线程的 RunLoop 发送消息，RunLoop会被唤醒，并从消息中取得这个 block，并在回调里执行这个 block。Runloop只处理主线程的block，dispatch 到其他线程仍然是由 libDispatch 处理的。
	
*	timer
*	网络请求
	关于网络请求的接口:最底层是CFSocket层，然后是CFNetwork将其封装，然后是NSURLConnection对CFNetwork进行面向对象的封装，NSURLSession 是 iOS7 中新增的接口，也用到NSURLConnection的loader线程。所以还是以NSURLConnection为例。
当开始网络传输时，NSURLConnection 创建了两个新线程：com.apple.NSURLConnectionLoader 和 com.apple.CFSocket.private。其中 CFSocket 线程是处理底层 socket 连接的。NSURLConnectionLoader 这个线程内部会使用 RunLoop 来接收底层 socket 的事件，并通过之前添加的 Source0 通知到上层的 Delegate。
	![图2](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/runloop_network_request.jpg)

<h3 id="6">runloop具体应用场合 （何时使用）</h3>

*	滑动和图片刷新
	当tableview的cell上有需要从网络获取的图片的时候，滚动tableView，异步线程会去加载图片，加载完成后主线程就会设置cell的图片，但是会造成卡顿。可以让设置图片的任务在CFRunLoopDefaultMode下进行，当滚动tableView的时候，RunLoop是在 UITrackingRunLoopMode 下进行，不去设置图片，而是当停止的时候，再去设置图片。
	
	```objc
	-(void)viewDidLoad {
  		[super viewDidLoad];
  		// 只在NSDefaultRunLoopMode下执行(刷新图片)
  		[self.myImageView performSelector:@selector(setImage:) 		withObject:[UIImage imageNamed:@""] afterDelay:ti 		inModes:@[NSDefaultRunLoopMode]];    
	}
	```

*	常驻子线程，保持一直处理事件
	为了保证线程长期运转，可以在子线程中加入RunLoop，并且给Runloop设置item，防止Runloop自动退出。
	例子来自：[AFNetworking](https://github.com/AFNetworking/AFNetworking)
	
	```objc
	+(void)networkRequestThreadEntryPoint:(id)__unused object {
    	@autoreleasepool {
        	[[NSThread currentThread] setName:@"AFNetworking"];
        	NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
        	[runLoop addPort:[NSMachPort port] forMode:NSDefaultRunLoopMode];
        	[runLoop run];
    	}
	}
	+(NSThread *)networkRequestThread {
   		static NSThread *_networkRequestThread = nil;
    	static dispatch_once_t oncePredicate;
    	dispatch_once(&oncePredicate, ^{
        	_networkRequestThread = [[NSThread alloc] initWithTarget:self selector:@selector(networkRequestThreadEntryPoint:) object:nil];
        [_networkRequestThread start];
    	});
    	return _networkRequestThread;
	}
	-(void)start {
    	[self.lock lock];
    	if ([self isCancelled]) {
        	[self performSelector:@selector(cancelConnection) onThread:[[self class] networkRequestThread] withObject:nil waitUntilDone:NO modes:[self.runLoopModes allObjects]];
    	} else if ([self isReady]) {
        	self.state = AFOperationExecutingState;
        	[self performSelector:@selector(operationDidStart) onThread:[[self class] networkRequestThread] withObject:nil waitUntilDone:NO modes:[self.runLoopModes allObjects]];
    	}
    	[self.lock unlock];
	}
	```

*	AsyncDisplayKit
	AsyncDisplayKit 是 Facebook 推出的用于保持界面流畅性的框架，其原理大致如下：

	UI 线程中一旦出现繁重的任务就会导致界面卡顿，这类任务通常分为3类：排版，绘制，UI对象操作。

	排版通常包括计算视图大小、计算文本高度、重新计算子式图的排版等操作。
绘制一般有文本绘制 (例如 CoreText)、图片绘制 (例如预先解压)、元素绘制 (Quartz)等操作。
	UI对象操作通常包括 UIView/CALayer 等 UI 对象的创建、设置属性和销毁。

	其中前两类操作可以通过各种方法扔到后台线程执行，而最后一类操作只能在主线程完成，并且有时后面的操作需要依赖前面操作的结果 （例如TextView创建时可能需要提前计算出文本的大小）。ASDK 所做的，就是尽量将能放入后台的任务放入后台，不能的则尽量推迟 (例如视图的创建、属性的调整)。

	为此，ASDK 创建了一个名为 ASDisplayNode 的对象，并在内部封装了 UIView/CALayer，它具有和 UIView/CALayer 相似的属性，例如 frame、backgroundColor等。所有这些属性都可以在后台线程更改，开发者可以只通过 Node 来操作其内部的 UIView/CALayer，这样就可以将排版和绘制放入了后台线程。但是无论怎么操作，这些属性总需要在某个时刻同步到主线程的 UIView/CALayer 去。

	ASDK 仿照 QuartzCore/UIKit 框架的模式，实现了一套类似的界面更新的机制：即在主线程的 RunLoop 中添加一个 Observer，监听了 kCFRunLoopBeforeWaiting 和 kCFRunLoopExit 事件，在收到回调时，遍历所有之前放入队列的待处理的任务，然后一一执行。
	具体的代码可以看这里：[_ASAsyncTransactionGroup](https://github.com/facebook/AsyncDisplayKit/blob/master/AsyncDisplayKit%2FDetails%2FTransactions%2F_ASAsyncTransactionGroup.m)。