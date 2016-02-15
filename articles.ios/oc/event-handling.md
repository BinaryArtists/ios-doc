## 事件处理，渲染过程

参考链接：
1. [About Events in iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009541-CH1-SW1)
2.

### 系统事件处理

1. 3类事件
  * 触摸事件（单点、多点、手势）
  * 传感器事件（加速度传感器）
  * 远程控制事件

2. [触摸事件的分发处理](http://blog.csdn.net/sakulafly/article/details/18766339)
  * 责任链/响应者链(事件传递机制)
  * 视图层（UIApplication/UIWindow/UIView）
  * UITouch/UIEvent
  * hit-test view
  * 第一响应者（First Responder）

3. [iOS事件机制(一)](http://www.cnblogs.com/zhw511006/p/3517248.html)

4. [从一个需求问题看iOS的事件处理](http://www.molotang.com/articles/1513.html)

5. [Learning Cocoa with Objective-C/Single-Window Applications/Event Handling](http://commons.oreilly.com/wiki/index.php/Learning_Cocoa_with_Objective-C/Single-Window_Applications/Event_Handling)
  * Events
    > The Event Cycle
    > Responders
    > First Responder
    > Event Routing
  * Dot View Application

### 系统渲染过程

1. [iOS 事件处理机制与图像渲染过程](http://www.cocoachina.com/ios/20151203/14549.html)

### 定制“事件处理”

----------

1. [Event handling between C++ and Objective C](http://stackoverflow.com/questions/14329218/event-handling-between-c-and-objective-c)

1) Dictionary of arrays of blocks where the key in the dictionary is the event being fired and then when the event manager gets the event it cycles over the array for that key and runs all the blocks

2) Set up a [KVO system](http://developer.apple.com/library/mac/#documentation/cocoa/conceptual/KeyValueObserving/Articles/KVOCompliance.html) where your events are your keys/values and your observers are your handlers

3) setup a [delegate-protocol](http://enroyed.com/ios/delegation-pattern-in-objective-c-and-writing-custom-delegates/) system so that your delegate is your handler and your protocol maps the events that can be fired

-----------

I assume question is related to the porting of Windows source code to Mac or iOS.

Not really full answer but you can start from here.

CreateEvent, ResetEvent, WaitForSingleObject => NSCondition Class Reference

Event is the unique feature of Windows but reasonably replaced with NSCondtion.

BeginThread => NSThread Class Reference
More specifically + (void)detachNewThreadSelector:(SEL)aSelector toTarget:(id)aTarget withObject:(id)anArgument method.

AfxBeginThread(WorkerThreadProc,NULL,THREAD_PRIORITY_NORMAL,0,0,NULL);
=>
[NSThread detachNewThreadSelector:@selector(WorkerThreadProc:) toTarget:self withObject:NULL];

PulseEvent is very difficult to port to Mac or iOS.

-----------

2. [uber/signals-ios](https://github.com/uber/signals-ios)

3. [JARinteractive/ESCObservable](https://github.com/JARinteractive/ESCObservable)

iOS eventing framework

4. 

### 其他

1. [How to add event handlers to the AppDelegate in Objective-C](http://easynativeextensions.com/how-to-add-event-handlers-to-the-appdelegate-in-objective-c/)

2. [Console Event Handling](http://www.codeproject.com/Articles/2357/Console-Event-Handling)

windows 下的线程有，消息队列

3. [An Event Pool](http://www.codeproject.com/Articles/6156/An-Event-Pool)

代码有参考意义

4. 客户端与服务器之间 [Server-Sent Events for iOS and Mac](https://github.com/neilco/EventSource)

It creates a long-lived, unidirectional HTTP channel between your Cocoa app and a web server so that your app can receive events from the server.

5.
