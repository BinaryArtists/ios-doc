## runtime

1. http://www.cnblogs.com/ygm900/p/3599081.html中的cocoa对象
2. 特性：kvo、kvc、method swizzling、block

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
