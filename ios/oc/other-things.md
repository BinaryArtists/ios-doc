# other things

## method & selector

1. Method
	* SEL
	* IMP
2. Selector
	* SEL
	* 方法名编码？针对特定对象，找到方法地址就可以调用方法？在内存中每个类的方法都存储在类对象中，每个方法都有一个与之对应的SEL类型的数据，根据一个SEL数据就可以找到对应的方法地址，进而调用方法。

## 反射机制

1. Class反射
	* 通过类名的字符串形式实例化对象
	```
	Class class NSClassFromString@(@"student");
	Student *stu = [[class alloc ]init];
	```
	* 将类名变成字符串
	```
	Class class =[Student class];
	NSString *className = NSStringFromClass(class);
	```
2. SEL反射
	同上

## NS/CF/CG/CA/UI这些前缀分别是什么含义

* 函数归属于属于cocoa Fundation框架
* 函数归属于属于core Fundation框架
* 函数归属于属于CoreGraphics.frameworks框架
* 函数归属于属于CoreAnimation.frameworks框架
* 函数归属于属于UIkit框架

## 怎么理解 Objective-C是动态运行时语言

* 主要是将数据类型的确定由编译时,推迟到了运行时。这个问题其实浅涉及到两个概念,运行时和多态。
* 运行时机制使我们直到运行时才去决定一个对象的类别,以及调用该类别对象指定方法。
* 多态:不同对象以自己的方式响应相同的消息的能力叫做多态

## 怎么理解 readwrite, readonly, assign, retain, copy, nonatomic属性修饰符

* readwrite 是可读可写特性;需要生成getter方法和setter方法;
* readonly 是只读特性 只会生成getter方法 不会生成setter方法 ,不希望属性在类外改变;
* assign 是赋值特性,setter方法将传入参数赋值给实例变量;仅设置变量时; assign用于基础数据类型,如NSInteger,double,bool;
* retain 表示持有特性,setter方法将传入参数先保留,再赋值,传入参数的引用计数retaincount会+1;
* copy 表示赋值特性,setter方法将传入对象复制一份;需要完全一份新的变量时;
* nonatomic 非原子操作,决定编译器生成的setter getter是否是原子操作;
* atomic表示多线程安全,一般使用 nonatomic

## 怎么理解 Notification、KVC、KVO、Delegate、block、Target-Action

[](https://www.objc.io/issues/7-foundation/communication-patterns/)
[KVO and KVC article](https://www.objc.io/issues/7-foundation/key-value-coding-and-observing/)

* KVO（Key-Value- Observing）：键值观察机制，它提供了观察对象的属性变化的方法；注册、反注册；一对多, 观察者模式
	- KVO is a mechanism to notify objects about property changes. 
* KVC(Key-Value-Coding)：是键值编码, 一个对象在调用setValue的时候，
	- 检查是否存在相应key的set方法，存在就调用set方法。
	- set方法不存在，就查找_key的成员变量是否存在，存在就直接赋值。
	- 如果_key没找到，就查找相同名称的key，存在就赋值。
	- 如果没有就调用valueForUndefinedkey和setValue：forUndefinedKey。
* Delegate: 通常发送者和接收者的关系是直接的一对一的关系。
	- 代理的目的是改变或传递控制链，以面向接口的方式回调
	- 可以减少框架复杂度。消息的发送者(sender)告知接收者(receiver)某个事件将要发生，delegate同意然后发送者响应事件，delegate机制使得接收者可以改变发送者的行为(这就是所谓的控制反转，反向依赖)。
* Notification: 观察者模式, 通常发送者和接收者的关系是间接的多对多关系。消息的发送者告知接收者事件已经发生或者将要发送，仅此而已，接收者并不能反过来影响发送者的行为。
	- Notifications can be used to send arbitrary messages and they can even contain a payload in form of their userInfo dictionary or by subclassing NSNotification. What makes notifications unique is that the sender and the recipient don’t have to know each other. They can be used to send information between very loosely coupled modules. Therefore, the communication is one-way – you cannot reply to a notification.

## 怎么理解 懒加载、延时加载

* 用时创建

## 怎么理解 category extension

* 类别: 在没有原类.m文件的基础上,给该类添加方法;
* 扩展:一种特殊形式的类别,主要在一个类的.m文件里声明和实现延展的作用,就是给某类添加私有方法或是私有变量。

## 类变量的@protected,@private,@public,@package声明各有什么含义?

* 变量的作用域不同。
	- @protected 该类和子类中访问，是默认的;
	- @private 只能在本类中访问;
	- @public 任何地方都能访问;
	- @package 本包内使用，跨包不可以

## 什么是谓词

* 谓词就是通过NSPredicate给定的逻辑条件作为约束条件，完成对数据的筛选。

## atomic和nonatomic的区别

* atomic提供多线程安全，防止读写未完成的时候被另外一个线程读写，造成数据错误。
* nonatomic在自己管理内存的环境中，解析的访问器保留并自动释放返回值，若指定了nonatomic，那么访问器只是简单的返回这个值。

## 理解 nil、Nil、NULL和 NSNull

* nil和C语言的NULL相同，在objc/objc.h中定义。nil表示Objective-C对象的值为空。在C语言中，指针的空值用NULL表示。在Objective-C中，nil对象调用任何方法表示什么也不执行，也不会崩溃。
* Nil:那么对于我们Objective-C开发来说，Nil也就代表((void *)0)。但是它是用于代表空类的. 比如：Class myClass = Nil;
* NULL: 在C语言中，NULL是无类型的，只是一个宏，它代表空. 这就是在C/C++中的空指针。对于我们Objective-C开发来说，NULL就表示((void*)0).
* NSNull:NSNull是继承于NSObject的类型。它是很特殊的类，它表示是空，什么也不存储，但是它却是对象，只是一个占位对象。使用场景就不一样了，比如说服务端接口中让我们在值为空时，传空。NSDictionry *parameters = @{@"arg1" : @"value1",@"arg2" : arg2.isEmpty ? [NSNull null] : arg2};

* NULL、nil、Nil这三者对于Objective-C中值是一样的，都是(void *)0，那么为什么要区分呢？又与NSNull之间有什么区别：

* NULL是宏，是对于C语言指针而使用的，表示空指针
* nil是宏，是对于Objective-C中的对象而使用的，表示对象为空
* Nil是宏，是对于Objective-C中的类而使用的，表示类指向空
* NSNull是类类型，是用于表示空的占位对象，与JS或者服务端的null类似的含意

## atomic 和 nonatomic 有何区别？

默认就是atomic

atomic 和 nonatomic 的区别在于，系统自动生成的 getter/setter 方法不一样。如果你自己写 getter/setter，那 atomic/nonatomic/retain/assign/copy 这些关键字只起提示作用，写不写都一样。

对于atomic的属性，系统生成的 getter/setter 会保证 get、set 操作的完整性，不受其他线程影响。比如，线程 A 的 getter 方法运行到一半，线程 B 调用了 setter：那么线程 A 的 getter 还是能得到一个完好无损的对象。

而nonatomic就没有这个保证了。所以，nonatomic的速度要比atomic快。

不过atomic可并不能保证线程安全。如果线程 A 调了 getter，与此同时线程 B 、线程 C 都调了 setter——那最后线程 A get 到的值，3种都有可能：可能是 B、C set 之前原始的值，也可能是 B set 的值，也可能是 C set 的值。同时，最终这个属性的值，可能是 B set 的值，也有可能是 C set 的值。

保证数据完整性——这个多线程编程的最大挑战之一——往往还需要借助其他手段。

## id类型是什么，instancetype是什么，有什么区别？

id类型：万能指针，能作为参数，方法的返回类型。
instancetype：只能作为方法的范围类型，并且返回的类型是当前定义类的类类型。

## 开发项目时你是怎么检查内存泄露

静态分析 analyze
instruments工具里面有个leak 可以动态分析

## 常见的出现内存循环引用的场景有哪些？

* 定时器（NSTimer）：NSTimer经常会被作为某个类的成员变量，而NSTimer初始化时要指定self为target，容易造成循环引用（self->timer->self）。 另外，若timer一直处于validate的状态，则其引用计数将始终大于0，因此在不再使用定时器以后，应该先调用invalidate方法
* block的使用：block在copy时都会对block内部用到的对象进行强引用(ARC)或者retainCount增1(非ARC)。在ARC与非ARC环境下对block使用不当都会引起循环引用问题， 一般表现为，某个类将block作为自己的属性变量，然后该类在block的方法体里面又使用了该类本身，简单说就是self.someBlock =Type var{[self dosomething];或者self.otherVar = XXX;或者_otherVar = …};出现循环的原因是：self->block->self或者self->block->_ivar（成员变量）
* 代理（delegate）：在委托问题上出现循环引用问题已经是老生常谈了，规避该问题的杀手锏也是简单到哭，一字诀：声明delegate时请用assign(MRC)或者weak(ARC)，千万别手贱玩一下retain或者strong，毕竟这基本逃不掉循环引用了！
