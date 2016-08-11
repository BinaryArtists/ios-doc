## runtime

1. http://www.cnblogs.com/ygm900/p/3599081.html中的cocoa对象
2. 特性：kvo、kvc、method swizzling、block

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
