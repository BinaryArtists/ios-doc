# NSOpertaionQueue和GCD之间

1. 先搞清两者的关系,NSOpertaionQueue用GCD构建封装的，是GCD的高级抽象
2. 个人理解：gcd 偏向于 过程式；operation 偏向于 对象操作
3. 因此，用面向对象的思想来看待operation，更适合异步操作之间的事务性，顺序行，依赖关系，NSOperationQueue支持KVO，意味着我们可以观察任务的执行状态。

## 关于NSOperation

NSOperation: 抽象类,不能直接使用,需要使用其子类.(类似的类还有核心动画)
两个常用子类: NSInvocationOperation(调用) 和 NSBlockOperation(块);
两者没有本质区别,后者使用 Block 的形式组织代码,使用相对方便.
NSInvocationOperation在调用start方法后，不会开启新的线程只会在当前线程中执行。
NSBlockOperation 在调用start方法后，如果封装的操作数>1会开辟多条线程执行 =1 只会在当前线程执行.
NSOperationQueue 创建的操作队列默认为全局队列，队列中的操作执行顺序是无序的，如果需要让它有序执行需要添加依赖关系。
// 操作op3依赖于操作op2 [op3 addDependency:op2];
// 操作op2依赖于操作op1 [op2 addDependency:op1];
同时可以设置最大并发数
NSOperationQueue NSOperation支持 取消暂停的操作 但是正在进行的的操作并不能取消，这一旦取消不可恢复.
NSOperationQueue支持KVO，可以监测operation是否正在执行（isExecuted）、是否结束（isFinished），是否取消（isCanceld）

## NSOperation queue?

存放NSOperation的集合类。不能说队列，不是严格的先进先出

## NSOperation与GCD的区别

GCD

GCD是iOS4.0推出的，主要针对多核cpu做了优化，是纯c语言的技术。
GCD是将任务（block）添加到队列（串行、并行、全局、主队列），并且以同步/异步的方式执行任务的函数。
GCD提供了一些NSOperation不具备的功能
一次性执行
延迟执行
调度组
GCD 是严格的队列，先进先出 FIFO；
NSOperation

NSOperation是iOS2.0推出的，iOS4.0以后又重写了NSOperation
NSOperation是将操作（异步的任务）添加到队列（并发队列），就会执行指定的函数
NSOperation提供的方便操作
最大并发数
队列的暂停和继续
取消所有的操作
指定操作之间的依赖关系依赖关系，可以让异步任务同步执行.
将KVO用于NSOperation中，监听一个operation是否完成。
能够设置NSOperation的优先级，能够使同一个并行队列中的任务区分先后地执行
对NSOperation进行继承，在这之上添加成员变量与成员方法，提高整个代码的复用度

## GCD与NSThread的区别

NSThread 通过 @selector 指定要执行的方法，代码分散
GCD 通过 block 指定要执行的代码，代码集中, 所有的代码写在一起的，让代码更加简单，易于阅读和维护
使用 GCD 不需要管理线程的创建/销毁/复用的过程！程序员不用关心线程的生命周期
如果要开多个线程 NSThread 必须实例化多个线程对象
NSThread 靠 NSObject 的分类方法实现的线程间通讯，

## 为什么要取消/恢复队列呢？

一般在内存警告后取消队列中的操作。
为了保证scorllView在滚动的时候流畅 通常在滚动开始时，暂停队列中的所有操作，滚动结束后，恢复操作。