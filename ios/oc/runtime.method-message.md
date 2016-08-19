## Objective-C Runtime 运行时之三：方法与消息

## runtime 消息机制

  * [runtime那些事儿（消息机制）](http://www.cocoachina.com/ios/20160301/15494.html)
  * [Runtime初涉之消息转发](http://www.cocoachina.com/ios/20151015/13769.html)

### 消息传递

[image](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/message_send_funct_invoke.png)

相关函数：
  * 对象在收到无法解读的消息后，首先会调用所属类的
  ```
  + (BOOL)resolveInstanceMethod:(SEL)sel
  ```

  > 这个方法在运行时，没有找到SEL的IML时就会执行。这个函数是给类利用class_addMethod添加函数的机会。

  ```
  OBJC_EXPORT BOOL class_addMethod(Class cls, SEL name, IMP imp,  const char *types)
  ```

  *

### 消息转发

Objc_msgSend方法的工作方式是为发送过来的方法查找恰当的方法实现，并且跳转到该实现中的方式工作，代码如下：
```
IMP lookUp(id obj, SEL selector) {
   Class c = object_getClass(obj);
   while(c) {
       for(int i = 0; i < c->numMethods; i++) {
           Method m = c->methods[i];
           if(m.selector == selector) {
               return m.imp;
           }
       }
       c = c->superclass;
   }
   return _objc_msgForward;
}
```

当向某个对象发送一条消息时，若该对象的方法列表以及它相应继承链上的方法列表都无法找到以该消息选择子作为key的方法实现时，则会触发消息转发机制。

1、动态方法解析
```
+ (BOOL)resolveInstanceMethod:(SEL)sel;
```
首先，当接受到未能识别的选择子时，运行时系统会调用该函数用以给对象一次机会来添加相应的方法实现，如果用户在该函数中动态添加了相应方法的实现，则跳转到方法的实现部分，并将该实现存入缓存中，以供下次调用。

2、备援接收者
```
- (id)forwardingTargetForSelector:(SEL)aSelector;
```
如果运行时在消息转发的第一步中未找到所调用方法的实现，那么当前接收者还有第二次机会进行未知选择子的处理。这时运行期系统会调用上述方法，并将未知选择子作为参数传入，该方法可以返回一个能处理该选择子的对象，运行时系统会根据返回的对象进行查找，若找到则跳转到相应方法的实现，则消息转发结束。

3、完整的消息转发
```
- (void)forwardInvocation:(NSInvocation *)anInvocation;
```
当运行时系统检测到第二步中用户未返回能处理相应选择子的对象时，那么来到这一步就要启动完整的消息转发机制了。该方法可以改变消息调用目标，运行时系统根据所改变的调用目标，向调用目标方法列表中查询对应方法的实现并实现跳转，这种方式和第二步的操作非常相似。当然你也可以修改方法的选择子，亦或者向所调用方法中追加一个参数等来跳转到相关方法的实现。

最后，如果消息转发的第三步还未能处理该未知选择子的话，那么最终会调用NSObject类的如下方法用以异常的抛出，表明该选择子最终未能处理。

```
- (void)doesNotRecognizeSelector:(SEL)aSelector;
```

下面附上完整的消息转发流程图：
[image](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/msg_dispatch_diagram.png)

### 方法缓存

Objective-C的Runtime机制是Mac和iOS程序中的核心，而objc_msgSend函数是Runtime的核心，进言之，这个函数的核心正是方法缓存。

在Objective-C的程序中，消息发送随处可见，如果对每一条消息都执行完整的消息搜索，那将会使程序变得异常迟钝。

解决方法就是缓存，每一个类都拥有一个哈希表与之关联,这个哈希表将选择器映射到方法实现。使用哈希表的初衷正是为了最大限度的提高读取速度，同时 objc_msgSend 使用了极其细致且高效的汇编源码来快速执行哈希表的检索，这使得在缓存模式下的消息发送仅维持在一个个位数的纳秒量级上。当然，任何消息在第一次使用的时候很慢，以后将会非常快。

我们提到的缓存，是用来提高多次读取最近使用资源的速度的，它通常也是有大小限制的。

### 方法缓存中的线程问题

在概念上，改变缓存的大小简单，就像这样
```
   bucket_t *newCache = malloc(newSize);
   copyEntries(newCache, class->cache);
   free(class->cache);
   class->cache = newCache;
```

Objective-C runtime 实际上在这里采用了一些捷径，但是不会将旧的条目拷贝到新的缓存区！毕竟它仅仅是缓存而已，没有必要保存数据，这些条目将在消息发送的时候再次被填充，所以，真实情况是这样的：
```
free(class->cache);
class->cache = malloc(newSize);
```

在单线程环境下，你需要做的仅有这些。当然Objective - C runtime也必须要支持多线程，也就是说所有这些代码都必须是线程安全的。任何给出的类的缓存，都可以从多个线程中被同时访问，所以这些代码必须考虑周全，确保可以应付这种场景。

写到这里的代码是无法处理多线程的情况的。在`释放旧的缓存到分配新的缓存之前`这段时间内，其他线程也许会访问这些已经失效的缓存指针，这会造成它使用的数据是垃圾数据，或者由于指定的内存并未映像物理地址出而立刻崩溃。

我们该如何解决这种问题？典型的保存共享数据的一种方法是加锁，代码如下：
```
lock(class->lock);
free(class->cache);
class->cache = malloc(newSize);
unlock(class->lock);
```
为此，包括读取在内的所有访问都会被这个锁控制。也就是说 *Objc_msgSend* 方法需要获得这个锁，查找缓存，然后放锁。每次进行加锁，解锁操作都会增加许多开销，考虑到缓存每次对自己的检索只需要几纳秒时间，这对性能的影响太大了。

我们也许会尝试通过一些其他方式关闭这个时间窗口（*释放旧缓存到分配新缓存这个时间窗*）。例如，对缓存先分配地址并赋值，然后再去释放旧的缓存如何？
```
bucket_t *oldCache = class->cache;
class->cache = malloc(newSize);
free(oldCache);
```
这会有一些帮助，但是并不能解决这个问题。另外一个线程也许会检索旧的缓存指针，然后在他可以访问内容之前通过系统先行占取这块缓存。这块旧的缓存在其他的线程再次运行之前被销毁，之前的问题再次出现。

如果加一个像这样的延迟呢？

[一下看原文吧：Objective-C Runtime中的并发内存分配](http://www.cocoachina.com/ios/20150706/12417.html)
