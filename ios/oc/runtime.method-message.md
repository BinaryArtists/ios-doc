## Objective-C Runtime 运行时之三：方法与消息

### 基础数据类型

  * SEL
    SEL又叫选择器，是表示一个方法的selector的指针，其定义如下：
    ```
    typedef struct objc_selector *SEL;
    ```
    objc_selector结构体的详细定义没有在头文件中找到。方法的selector用于表示运行时方 法的名字。Objective-C在编译时，会依据每一个方法的名字、参数序列，生成一个唯一的整型标识(Int类型的地址)，这个标识就是SEL。如下 代码所示：
    ```
    SEL sel1 = @selector(method1);
    NSLog(@"sel : %p", sel1);
    ```
    上面的输出为：
    ```
    2014-10-30 18:40:07.518 RuntimeTest[52734:466626] sel : 0x100002d72
    ```
    两个类之间，不管它们是父类与子类的关系，还是之间没有这种关系，只要方法名相同，那么方法的SEL就是一样的。每一个方法都对应着一个SEL。所以在 Objective-C同一个类(及类的继承体系)中，不能存在2个同名的方法，即使参数类型不同也不行。相同的方法只能对应一个SEL。这也就导致 Objective-C在处理相同方法名且参数个数相同但类型不同的方法方面的能力很差。如在某个类中定义以下两个方法：

    ```
    - (void)setWidth:(int)width;
    - (void)setWidth:(double)width;
    ```

    当然，不同的类可以拥有相同的selector，这个没有问题。不同类的实例对象执行相同的selector时，会在各自的方法列表中去根据selector去寻找自己对应的IMP。

    工程中的所有的SEL组成一个Set集合，Set的特点就是唯一，因此SEL是唯一的。因此，如果我们想到这个方法集合中查找某个方法时，只需要去 找到这个方法对应的SEL就行了，SEL实际上就是根据方法名hash化了的一个字符串，而对于字符串的比较仅仅需要比较他们的地址就可以了，可以说速度 上无语伦比！！但是，有一个问题，就是数量增多会增大hash冲突而导致的性能下降（或是没有冲突，因为也可能用的是perfect hash）。但是不管使用什么样的方法加速，如果能够将总量减少（多个方法可能对应同一个SEL），那将是最犀利的方法。那么，我们就不难理解，为什么 SEL仅仅是函数名了。

    本质上，SEL只是一个指向方法的指针（准确的说，只是一个根据方法名hash化了的KEY值，能唯一代表一个方法），它的存在只是为了加快方法的查询速度。这个查找过程我们将在下面讨论。

    我们可以在运行时添加新的selector，也可以在运行时获取已存在的selector，我们可以通过下面三种方法来获取SEL:
    ```
    1. sel_registerName函数

    2. Objective-C编译器提供的@selector()

    3. NSSelectorFromString()方法
    ```

  * IMP
    IMP实际上是一个函数指针，指向方法实现的首地址。其定义如下：
    ```
    id (*IMP)(id, SEL, ...)
    ```
    这个函数使用当前CPU架构实现的标准的C调用约定。第一个参数是指向self的指针(如果是实例方法，则是类实例的内存地址；如果是类方法，则是指向元类的指针)，第二个参数是方法选择器(selector)，接下来是方法的实际参数列表。

    前面介绍过的SEL就是为了查找方法的最终实现IMP的。由于每个方法对应唯一的SEL，因此我们可以通过SEL方便快速准确地获得它所对应的 IMP，查找过程将在下面讨论。取得IMP后，我们就获得了执行这个方法代码的入口点，此时，我们就可以像调用普通的C语言函数一样来使用这个函数指针 了。

    通过取得IMP，我们可以跳过Runtime的消息传递机制，直接执行IMP指向的函数实现，这样省去了Runtime消息传递过程中所做的一系列查找操作，会比直接向对象发送消息高效一些。

  * Method
    介绍完SEL和IMP，我们就可以来讲讲Method了。Method用于表示类定义中的方法，则定义如下：
    ```
    typedef struct objc_method *Method;

    struct objc_method {
        SEL method_name                 OBJC2_UNAVAILABLE;  // 方法名
        char *method_types                  OBJC2_UNAVAILABLE;
        IMP method_imp                      OBJC2_UNAVAILABLE;  // 方法实现
    }
    ```
    我们可以看到该结构体中包含一个SEL和IMP，实际上相当于在SEL和IMP之间作了一个映射。有了SEL，我们便可以找到对应的IMP，从而调用方法的实现代码。具体操作流程我们将在下面讨论。

  * objc_method_description
    objc_method_description定义了一个Objective-C方法，其定义如下：
    ```
    struct objc_method_description { SEL name; char * types; };
    ```

### 方法相关操作函数

Runtime提供了一系列的方法来处理与方法相关的操作。包括方法本身及SEL。本节我们介绍一下这些函数。

  * 方法
    方法操作相关函数包括下以：
    ```
    // 调用指定方法的实现
    id method_invoke ( id receiver, Method m, ... );

    // 调用返回一个数据结构的方法的实现
    void method_invoke_stret ( id receiver, Method m, ... );

    // 获取方法名
    SEL method_getName ( Method m );

    // 返回方法的实现
    IMP method_getImplementation ( Method m );

    // 获取描述方法参数和返回值类型的字符串
    const char * method_getTypeEncoding ( Method m );

    // 获取方法的返回值类型的字符串
    char * method_copyReturnType ( Method m );

    // 获取方法的指定位置参数的类型字符串
    char * method_copyArgumentType ( Method m, unsigned int index );

    // 通过引用返回方法的返回值类型字符串
    void method_getReturnType ( Method m, char *dst, size_t dst_len );

    // 返回方法的参数的个数
    unsigned int method_getNumberOfArguments ( Method m );

    // 通过引用返回方法指定位置参数的类型字符串
    void method_getArgumentType ( Method m, unsigned int index, char *dst, size_t dst_len );

    // 返回指定方法的方法描述结构体
    struct objc_method_description * method_getDescription ( Method m );

    // 设置方法的实现
    IMP method_setImplementation ( Method m, IMP imp );

    // 交换两个方法的实现
    void method_exchangeImplementations ( Method m1, Method m2 );
    ```
    ● method_invoke函数，返回的是实际实现的返回值。参数receiver不能为空。这个方法的效率会比method_getImplementation和method_getName更快。
    ● method_getName函数，返回的是一个SEL。如果想获取方法名的C字符串，可以使用sel_getName(method_getName(method))。
    ● method_getReturnType函数，类型字符串会被拷贝到dst中。
    ● method_setImplementation函数，注意该函数返回值是方法之前的实现。

  * 方法选择器
    选择器相关的操作函数包括：
    ```
    // 返回给定选择器指定的方法的名称
    const char * sel_getName ( SEL sel );

    // 在Objective-C Runtime系统中注册一个方法，将方法名映射到一个选择器，并返回这个选择器
    SEL sel_registerName ( const char *str );

    // 在Objective-C Runtime系统中注册一个方法
    SEL sel_getUid ( const char *str );

    // 比较两个选择器
    BOOL sel_isEqual ( SEL lhs, SEL rhs );
    ```
    ● sel_registerName函数：在我们将一个方法添加到类定义时，我们必须在Objective-C Runtime系统中注册一个方法名以获取方法的选择器。

### 方法调用流程

在Objective-C中，消息直到运行时才绑定到方法实现上。编译器会将消息表达式[receiver message]转化为一个消息函数的调用，即objc_msgSend。这个函数将消息接收者和方法名作为其基础参数，如以下所示：
```
objc_msgSend(receiver, selector)
```
如果消息中还有其它参数，则该方法的形式如下所示：
```
objc_msgSend(receiver, selector, arg1, arg2, ...)
```
这个函数完成了动态绑定的所有事情：

1. 首先它找到selector对应的方法实现。因为同一个方法可能在不同的类中有不同的实现，所以我们需要依赖于接收者的类来找到的确切的实现。

2. 它调用方法实现，并将接收者对象及方法的所有参数传给它。

3. 最后，它将实现返回的值作为它自己的返回值。

消息的关键在于我们前面章节讨论过的结构体objc_class，这个结构体有两个字段是我们在分发消息的关注的：

1. 指向父类的指针

2. 一个类的方法分发表，即methodLists。

当我们创建一个新对象时，先为其分配内存，并初始化其成员变量。其中isa指针也会被初始化，让对象可以访问类及类的继承体系。

下图演示了这样一个消息的基本框架：

当消息发送给一个对象时，objc_msgSend通过对象的isa指针获取到类的结构体，然后在方法分发表里面查找方法的selector。如果 没有找到selector，则通过objc_msgSend结构体中的指向父类的指针找到其父类，并在父类的分发表里面查找方法的selector。依 此，会一直沿着类的继承体系到达NSObject类。一旦定位到selector，函数会就获取到了实现的入口点，并传入相应的参数来执行方法的具体实 现。如果最后没有定位到selector，则会走消息转发流程，这个我们在后面讨论。

为了加速消息的处理，运行时系统缓存使用过的selector及对应的方法的地址。这点我们在前面讨论过，不再重复。

隐藏参数

objc_msgSend有两个隐藏参数：

1. 消息接收对象

2. 方法的selector

这两个参数为方法的实现提供了调用者的信息。之所以说是隐藏的，是因为它们在定义方法的源代码中没有声明。它们是在编译期被插入实现代码的。

虽然这些参数没有显示声明，但在代码中仍然可以引用它们。我们可以使用self来引用接收者对象，使用_cmd来引用选择器。如下代码所示：
```
- strange
{
    id  target = getTheReceiver();
    SEL method = getTheMethod();

    if ( target == self || method == _cmd )
        return nil;
    return [target performSelector:method];
}
```
当然，这两个参数我们用得比较多的是self，_cmd在实际中用得比较少。

获取方法地址

Runtime中方法的动态绑定让我们写代码时更具灵活性，如我们可以把消息转发给我们想要的对象，或者随意交换一个方法的实现等。不过灵活性的提 升也带来了性能上的一些损耗。毕竟我们需要去查找方法的实现，而不像函数调用来得那么直接。当然，方法的缓存一定程度上解决了这一问题。

我们上面提到过，如果想要避开这种动态绑定方式，我们可以获取方法实现的地址，然后像调用函数一样来直接调用它。特别是当我们需要在一个循环内频繁地调用一个特定的方法时，通过这种方式可以提高程序的性能。

NSObject类提供了methodForSelector:方法，让我们可以获取到方法的指针，然后通过这个指针来调用实现代码。我们需要将methodForSelector:返回的指针转换为合适的函数类型，函数参数和返回值都需要匹配上。

我们通过以下代码来看看methodForSelector:的使用：
```
void (*setter)(id, SEL, BOOL);
int i;

setter = (void (*)(id, SEL, BOOL))[target
    methodForSelector:@selector(setFilled:)];
for ( i = 0 ; i < 1000 ; i++ )
    setter(targetList[i], @selector(setFilled:), YES);
```
这里需要注意的就是函数指针的前两个参数必须是id和SEL。

当然这种方式只适合于在类似于for循环这种情况下频繁调用同一方法，以提高性能的情况。另外，methodForSelector:是由Cocoa运行时提供的；它不是Objective-C语言的特性。

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

### 消息转发

当一个对象能接收一个消息时，就会走正常的方法调用流程。但如果一个对象无法接收指定消息时，又会发生什么事呢？默认情况下，如果是以 [object message]的方式调用方法，如果object无法响应message消息时，编译器会报错。但如果是以perform…的形式来调用，则需要等到运 行时才能确定object是否能接收message消息。如果不能，则程序崩溃。

通常，当我们不能确定一个对象是否能接收某个消息时，会先调用respondsToSelector:来判断一下。如下代码所示：
```
if ([self respondsToSelector:@selector(method)]) {
    [self performSelector:@selector(method)];
}
```
不过，我们这边想讨论下不使用respondsToSelector:判断的情况。这才是我们这一节的重点。

当一个对象无法接收某一消息时，就会启动所谓”消息转发(message forwarding)“机制，通过这一机制，我们可以告诉对象如何处理未知的消息。默认情况下，对象接收到未知的消息，会导致程序崩溃，通过控制台，我们可以看到以下异常信息：
```
-[SUTRuntimeMethod method]: unrecognized selector sent to instance 0x100111940
*** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: '-[SUTRuntimeMethod method]: unrecognized selector sent to instance 0x100111940'
```
这段异常信息实际上是由NSObject的”doesNotRecognizeSelector”方法抛出的。不过，我们可以采取一些措施，让我们的程序执行特定的逻辑，而避免程序的崩溃。

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

### 参考
  * [runtime那些事儿（消息机制）](http://www.cocoachina.com/ios/20160301/15494.html)
  * [Runtime初涉之消息转发](http://www.cocoachina.com/ios/20151015/13769.html)
  * [Objective-C Runtime 运行时之三：方法与消息](http://www.cocoachina.com/ios/20141106/10150.html)
