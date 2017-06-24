# 讨论 NSString的实现

## NSCFConstantString

字符串常量，放在常量区，对其retain或者release不影响它的引用计数，程序结束后释放。比如：用字面量语法创建出来的string。

## NSTaggedPointerString

Tagged Point，标签指针，苹果在64位环境下对NSString和NSNumber做的一些优化，简单来说就是把对象的内容存放在了指针里，这样就不需要在堆内存里在开辟一块空间存放对象了，一般用来优化长度较小的内容。

### Tagged Pointer

1. 【缘起】CPU体系结构：iPhone5s配备了首个采用64位架构的A7双核处理器，为了节省内存和提高执行效率，苹果提出了Tagged Pointer的概念
    * 从32位到64位，所有指针地址变量内存，增长一倍
2. 【缘因】
    * 为了改进内存占用和效率问题，苹果提出了Tagged Pointer对象。由于NSNumber、NSDate一类的变量本身的值需要占用的内存大小常常不需要8个字节，拿整数来说，4个字节所能表示的有符号整数就可以达到20多亿（注：2^31=2147483648，另外1位作为符号位)，对于绝大多数情况都是可以处理的。
3. 【缘定】
    * 所以引入了Tagged Pointer对象之后，64位这类“指针”, 一个对象的指针拆成两部分，一部分直接保存数据，另一部分作为特殊标记，表示这是一个特别的指针，不指向任何一个地址。
    * 可见，当8字节可以承载用于表示的数值时，系统就会以Tagged Pointer的方式生成指针，如果8字节承载不了时，则又用以前的方式来生成普通的指针。
4. 其他
    * 我们也可以在WWDC2013的《Session 404 Advanced in Objective-C》视频中，看到苹果对于Tagged Pointer特点的介绍：
        - Tagged Pointer专门用来存储小的对象，例如NSNumber和NSDate
        - Tagged Pointer指针的值不再是地址了，而是真正的值。所以，实际上它不再是一个对象了，它只是一个披着对象皮的普通变量而已。所以，它的内存并不存储在堆中，也不需要malloc和free。
        - 在内存读取上有着3倍的效率，创建时比以前快106倍。
    * 因为Tagged Pointer并不是真正的对象，我们在使用时需要注意不要直接访问其isa变量。

[采用Tagged Pointer的字符串](http://ios.jobbole.com/82456/)

## NSCFString

这种string就和普通的对象很像了，储存在堆上，有正常的引用计数，需要程序员分配释放。所以references = cstr时，会打印出null，cstr出了方法作用域在runloop结束时就被autoreleasepool释放了。