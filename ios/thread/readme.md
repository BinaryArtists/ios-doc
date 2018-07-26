## 线程

## NSThread GCD NSOperation 使用场景

Use NSThread (or even the pthreads API) when you want or need to have direct control over the threads you create, e.g. you need fine-grained control over thread priorities or are interfacing with some other subsystem that vends/consumes thread objects directly and you need to stay on the same page with it. Such instances are rare, but they do occur, particularly in real-time applications.

Use GCD when your task lends itself well to simple parallelization, e.g. you just want to toss some work "into the background" with very little additional work, you have some data structures that you merely wish to serialize access to (and serial queues are great for doing that in a lockless fashion), you have some for loops that would lend themselves well to parallelization with dispatch_apply(), you have some data sources / timers that GCD's sources API will enable you to deal with easily in the background, etc etc. GCD is quite powerful and you can use it for a lot more than this, but these are all relative 'no brainer' scenarios where you don't want to get caught up in the initialization and setup tasks so much as simply "do basic stuff in parallel".

Use NSOperation when you're already up at the Cocoa API layer (vs writing in straight C to the POSIX APIs) and have more complex operations you want to parallelize. NSOperation allows for subclassing, arbitrarily complex dependency graphs, cancellation and a supports a number of other higher-level semantics that may be useful to you. NSOperation actually uses GCD under the covers so it's every bit as multi-core, multi-thread capable as GCD, though it also brings the Foundation framework along for the ride so if you're hacking at the POSIX layer, you probably want to use option #2.

## iOS 下不同线程模型的性能比较