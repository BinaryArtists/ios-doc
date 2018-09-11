# Objective-C Runtime

[runtime源码](http://opensource.apple.com/tarballs/objc4/)

Objective-C 语言是一门动态语言，编译器不需要关心接受消息的对象是何种类型，接收消息的对象问题也要在运行时处理。

## 应用

可以在运行时，在不继承也不category的情况下，为各种类（包括系统的类）做很多操作，具体包括：

*增加*

* 增加函数:class_addMethod
* 增加实例变量:class_addIvar
* 增加属性:@dynamic标签，或者class_addMethod，因为属性其实就是由getter和setter函数组成
* 增加Protocol:class_addProtocol (说实话我真不知道动态增加一个protocol有什么用,-_-!!)
* 关联对象 [Associated Objects](https://nshipster.cn/associated-objects/), 给分类添加属性

*获取*

* 获取函数列表及每个函数的信息(函数指针、函数名等等):class_getClassMethod method_getName ...
* 获取属性列表及每个属性的信息:class_copyPropertyList property_getName
* 获取类本身的信息,如类名等：class_getName class_getInstanceSize
* 获取变量列表及变量信息：class_copyIvarList
* 获取变量的值
* 动态获取 class 和 slector
```
NSClassFromString(@"MyClass");
NSSelectorFromString(@"showShareActionSheet");
```
* * KVC、KVO  [About Key-Value Coding](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/KeyValueCoding/)

*替换*

* 将实例替换成另一个类：object_setClass
* 将函数替换成一个函数实现：class_replaceMethod
	- 方法调配 [Method Swizzling](https://nshipster.com/method-swizzling/)
* 直接通过char *格式的名称来修改变量的值，而不是通过变量


## 案例

```
Aspects（AOP必备，“取缔” baseVC，无侵入埋点）
MJExtension（JSON 转 model，一行代码实现 NSCoding 协议的自动归档和解档）
JSPatch（动态下发 JS 进行热修复）
NullSafe（防止因发 unrecognised messages 给 NSNull 导致的崩溃）
UITableView-FDTemplateLayoutCell（自动计算并缓存 table view 的 cell 高度）
UINavigationController+FDFullscreenPopGesture（全屏滑动返回）
```

## 详解

* “类对象” [NSProxy Foundation | Apple Developer Documentation](https://developer.apple.com/documentation/foundation/nsproxy)


### 基本数据结构

* SEL

* id


* Class



### 运行机制 - 动态方法解析


### 运行机制 - 消息发送

* 消息发送 [Messaging](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)

### 运行机制 - 消息转发

* 消息转发 [Message Forwarding](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html#//apple_ref/doc/uid/TP40008048-CH105-SW1)

### 关键的 Cocoa 类对象



