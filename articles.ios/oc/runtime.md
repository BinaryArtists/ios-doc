## runtime

1. http://www.cnblogs.com/ygm900/p/3599081.html中的cocoa对象
2. 特性：kvo、kvc、method swizzling、block
3. [Runtime全方位装逼指南](http://www.cocoachina.com/ios/20160523/16386.html)

### Runtime 是什么？

  1. 运行时系统

### Objective-C 对象模型

* isa指针
  1. 对象是类的一个实例，类也是一个对象，所以它也必须是另一个类的实例，这个类就是元类（metaclass）
  2. 元类：（也是个对象）
    > 保存了类方法的列表
    > 为了设计上的完整，所有的元类的isa指针都会指向一个根元类（root metaclass），根元类本身的isa指针指向自己。
    > 一个对象能够接收的消息列表是保存在它所对应的类中。
  3. 类的成员变量

``` 类的成员在结构体里的排列
isa 指针
NSObject 成员变量
NSObject 子类的成员变量
NSObject 子类的子类的。。。
。。。
父类 成员变量
类本身 成员变量
```
    > 由于该结构体的大小并不能动态变化，所以无法在运行时动态给对象增加成员变量
    > 相应的，对象的方法定义都保存在类的可变区域中，在Objective-C 1.0中，看到方法定义列表是一个methodLists指针的指针。通过修改该指针指向的指针的值，就可以动态为某一个类增加成员方法，Category实现的原理。
    > 特别说明一下：通过objc_setAssociatedObject和objc_getAssociatedObject方法可以变相地给对象增加乘员变量，但由于实现机制不一样，所以并不是真正改变了对象的内存结构。

* 对象模型的应用
  1. 动态创建对象
    > import runtime 相关的头文件：objc/runtime.h
    > 使用 objc_allocateClassPair方法创建新的类
    > 使用 class_addMethod 方法来给类增加新的方法
    > 使用 objc_registerClassPair 来注册新的类
    > 使用 object_getClass 方法来获得对象的isa指针所指向的对象

  2. isa swizzling的应用
    > [kvo就利用了动态修改isa指针的值的技术](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/KeyValueObserving/Articles/KVOImplementation.html)
    > method swizzling：（class_replaceMethod替换类方法的定义）（method_exchangeImplementations交换两个方法的实现）（method_setImplementation设置一个方法的实现）

### Tagged Pointer 对象

主要解决32位 到 64位迁移时，带来的内存使用、性能上的损失。

简述：将一个对象指针拆成两部分，一部分直接保存数据，一部分作为特殊标记，表示这是一个特别的指针，不指向任何一个地址

### block 对象模型

block的数据结构定义：

[Block_private.h](https://llvm.org/svn/llvm-project/compiler-rt/tags/Apple/Libcompiler_rt-10/BlocksRuntime/Block_private.h)

### runtime 语法表达式

1. objc_msgSend

Objective-C 中的方法调用，不是简单的方法调用，而是发送消息，也就是说，其实 [receiver message] 会被编译器转化为: objc_msgSend(receiver, selector)

想要证明，直接看预编译文件即可，Xcode－Product－Perform Action－Preprocess，或者用下面的命令：
```
clang -rewrite-objc MyClass.m
```
该命令会生成MyClass.cpp，打开查阅。

2. SEL

SEL 被定义在 objc/objc.h 目录下：
```
typedef struct objc_selector *SEL;
```
其实它就是个映射到方法的C字符串，你可以用 Objective-C 编译器命令 @selector() 或者 Runtime 系统的 sel_registerName 函数来获得一个 SEL 类型的方法选择器。

3. id

与 SEL 一样，id 也被定义在 objc/objc.h 目录下：
```
typedef struct objc_object *id;
```
id 是一个结构体指针类型，它可以指向 Objective-C 中的任何对象。objc_object 结构体定义如下：
```
struct objc_object { Class isa OBJC_ISA_AVAILABILITY;};
```
我们通常所说的对象，就长这个样子，这个结构体只有一个成员变量 isa，对象可以通过 isa 指针找到其所属的类。isa 是一个 Class 类型的成员变量，那么 Class 又是什么呢？

4. Class

Class 也是一个结构体指针类型：
```
typedef struct objc_class *Class;
```
objc_class 结构体是酱紫滴：
```
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;
#if !__OBJC2__
    Class super_class   OBJC2_UNAVAILABLE;
    const char *name   OBJC2_UNAVAILABLE;
    long version     OBJC2_UNAVAILABLE;
    long info     OBJC2_UNAVAILABLE;
    long instance_size   OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars  OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists  OBJC2_UNAVAILABLE;
    struct objc_cache *cache    OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols   OBJC2_UNAVAILABLE;
#endif
} OBJC2_UNAVAILABLE;
```

我们通常说的类就长这样子：
·Class 也有一个 isa 指针，指向其所属的元类（meta）。
·super_class：指向其超类。
·name：是类名。
·version：是类的版本信息。
·info：是类的详情。
·instance_size：是该类的实例对象的大小。
·ivars：指向该类的成员变量列表。
·methodLists：指向该类的实例方法列表，它将方法选择器和方法实现地址联系起来。methodLists 是指向 ·objc_method_list 指针的指针，也就是说可以动态修改 *methodLists* 的值来添加成员方法，这也是 Category 实现的原理，同样解释了 Category 不能添加属性的原因。
·cache：Runtime 系统会把被调用的方法存到 cache 中（理论上讲一个方法如果被调用，那么它有可能今后还会被调用），下次查找的时候效率更高。
·protocols：指向该类的协议列表。
说到这里有点乱了，我们来捋一下，当我们调用一个方法时，其运行过程大致如下：

首先，Runtime 系统会把方法调用转化为消息发送，即 objc_msgSend，并且把方法的调用者，和方法选择器，当做参数传递过去.

此时，方法的调用者会通过 isa 指针来找到其所属的类，然后在 cache 或者 methodLists 中查找该方法，找得到就跳到对应的方法去执行。

如果在类中没有找到该方法，则通过 super_class 往上一级超类查找（如果一直找到 NSObject 都没有找到该方法的话，这种情况，我们放到后面消息转发的时候再说）。

前面我们说 methodLists 指向该类的实例方法列表，实例方法即-方法，那么类方法（+方法）存储在哪儿呢？类方法被存储在元类中，Class 通过 isa 指针即可找到其所属的元类。
[image](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/super_class.jpg)

上图实线是 super_class 指针，虚线是 isa 指针。根元类的超类是NSObject，而 isa 指向了自己。NSObject 的超类为 nil，也就是它没有超类。

5. objc_msgSendSuper

编译器会根据情况在 objc_msgSend，objc_msgSend_stret，objc_msgSendSuper，objc_msgSendSuper_stret 或 objc_msgSend_fpret 五个方法中选择一个来调用。如果消息是传递给超类，那么会调用 objc_msgSendSuper 方法，如果消息返回值是数据结构，就会调用 objc_msgSendSuper_stret 方法，如果返回值是浮点数，则调用 objc_msgSend_fpret 方法。

这里我们重点说一下 objc_msgSendSuper，objc_msgSendSuper 函数原型如下：
```
OBJC_EXPORT void objc_msgSendSuper(void /* struct objc_super *super, SEL op, ... */ )
```
当我们调用 [super selector] 时，Runtime 会调用 objc_msgSendSuper 方法，objc_msgSendSuper 方法有两个参数，super 和 op，Runtime 会把 selector 方法选择器赋值给 op。而 super 是一个 objc_super 结构体指针，objc_super 结构体定义如下：
```
struct objc_super {
    /// Specifies an instance of a class.
    __unsafe_unretained id receiver;
    /// Specifies the particular superclass of the instance to message.
#if !defined(__cplusplus)  &&  !__OBJC2__
    /* For compatibility with old objc-runtime.h header */
    __unsafe_unretained Class class;
#else
    __unsafe_unretained Class super_class;
#endif
    /* super_class is the first class to search */
};
```
Runtime 会创建一个 objc_spuer 结构体变量，将其地址作为参数（super）传递给 objc_msgSendSuper，并且将 self 赋值给 receiver：super—>receiver=self。

举个栗子，问下面的代码输出什么：
```
@implementation Son : Father
- (id)init
{
    self = [super init];
    if (self)
    {
        NSLog(@"%@", NSStringFromClass([self class]));
        NSLog(@"%@", NSStringFromClass([super class]));
    }
    return self;
}
@end
```
答案是全部输出 Son。

使用 clang 重写命令，发现上述代码被转化为:
```
NSLog((NSString *)&__NSConstantStringImpl__var_folders_gm_0jk35cwn1d3326x0061qym280000gn_T_main_a5cecc_mi_0, NSStringFromClass(((Class (*)(id, SEL))(void *)objc_msgSend)((id)self, sel_registerName("class"))));
NSLog((NSString *)&__NSConstantStringImpl__var_folders_gm_0jk35cwn1d3326x0061qym280000gn_T_main_a5cecc_mi_1, NSStringFromClass(((Class (*)(__rw_objc_super *, SEL))(void *)objc_msgSendSuper)((__rw_objc_super){ (id)self, (id)class_getSuperclass(objc_getClass("Son")) }, sel_registerName("class"))));
```

当调用 [super class] 时，会转换成 objc_msgSendSuper 函数：

第一步先构造 objc_super 结构体，结构体第一个成员就是 self。第二个成员是 (id)class_getSuperclass(objc_getClass(“Son”)).
第二步是去 Father 这个类里去找 - (Class)class，没有，然后去 NSObject 类去找，找到了。最后内部是使用 objc_msgSend(objc_super->receiver, @selector(class)) 去调用，此时已经和 [self class] 调用相同了，所以两个输出结果都是 Son。

6. 对象关联

对象关联允许开发者对已经存在的类在 Category 中添加自定义的属性：
```
OBJC_EXPORT void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy)
__OSX_AVAILABLE_STARTING(__MAC_10_6, __IPHONE_3_1);
```
·object 是源对象

·value 是被关联的对象

·key 是关联的键，objc_getAssociatedObject 方法通过不同的 key 即可取出对应的被关联对象

·policy 是一个枚举值，表示关联对象的行为，从命名就能看出各个枚举值的含义：
```
typedef OBJC_ENUM(uintptr_t, objc_AssociationPolicy) {
    OBJC_ASSOCIATION_ASSIGN = 0,           /**< Specifies a weak reference to the associated object. */
    OBJC_ASSOCIATION_RETAIN_NONATOMIC = 1, /**< Specifies a strong reference to the associated object.
                                            *   The association is not made atomically. */
    OBJC_ASSOCIATION_COPY_NONATOMIC = 3,   /**< Specifies that the associated object is copied.
                                            *   The association is not made atomically. */
    OBJC_ASSOCIATION_RETAIN = 01401,       /**< Specifies a strong reference to the associated object.
                                            *   The association is made atomically. */
    OBJC_ASSOCIATION_COPY = 01403          /**< Specifies that the associated object is copied.
                                            *   The association is made atomically. */
};
```
要取出被关联的对象使用 objc_getAssociatedObject 方法即可，要删除一个被关联的对象，使用 objc_setAssociatedObject 方法将对应的 key 设置成 nil 即可：
```
objc_setAssociatedObject(self, associatedKey, nil, OBJC_ASSOCIATION_COPY_NONATOMIC);
objc_removeAssociatedObjects 方法将会移除源对象中所有的关联对象.
```

8. 自动归档

略

9. 动态方法解析

前面我们留下了一点东西没说，那就是如果某个对象调用了不存在的方法时会怎么样，一般情况下程序会crash，错误信息类似下面这样：

unrecognized selector sent to instance 0x7fd0a141afd0

但是在程序crash之前，Runtime 会给我们动态方法解析的机会，消息发送的步骤大致如下：

1.检测这个 selector 是不是要忽略的。比如 Mac OS X 开发，有了垃圾回收就不理会 retain，release 这些函数了

2.检测这个 target 是不是 nil 对象。ObjC 的特性是允许对一个 nil 对象执行任何一个方法不会 Crash，因为会被忽略掉

3.如果上面两个都过了，那就开始查找这个类的 IMP，先从 cache 里面找，完了找得到就跳到对应的函数去执行

如果 cache 找不到就找一下方法分发表

4.如果分发表找不到就到超类的分发表去找，一直找，直到找到NSObject类为止

如果还找不到就要开始进入消息转发了，消息转发的大致过程如图：
[image](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/%E6%B6%88%E6%81%AF%E8%BD%AC%E5%8F%91.jpg)
1.进入 resolveInstanceMethod: 方法，指定是否动态添加方法。若返回NO，则进入下一步，若返回YES，则通过 class_addMethod 函数动态地添加方法，消息得到处理，此流程完毕。

2.resolveInstanceMethod: 方法返回 NO 时，就会进入 forwardingTargetForSelector: 方法，这是 Runtime 给我们的第二次机会，用于指定哪个对象响应这个 selector。返回nil，进入下一步，返回某个对象，则会调用该对象的方法。

3.若 forwardingTargetForSelector: 返回的是nil，则我们首先要通过 methodSignatureForSelector: 来指定方法签名，返回nil，表示不处理，若返回方法签名，则会进入下一步。

4.当第 methodSignatureForSelector: 方法返回方法签名后，就会调用 forwardInvocation: 方法，我们可以通过 anInvocation 对象做很多处理，比如修改实现方法，修改响应对象等。
