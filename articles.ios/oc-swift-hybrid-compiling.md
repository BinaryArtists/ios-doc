## Objective-C与swift混编

参考链接：
1. []()


例子工程：[SwiftProject](https://github.com/BinaryArtists/Musician)（测试代码，主要看SwiftProjectTests target中）

### 目录
  * [1. 源文件](#1)
    * [1.1 OC调用swift](#1.1)
      * 1.1.1 类继承
      * 1.1.2 类型兼容
    * [1.2 swift调用OC](#1.2)
  * [2. framework](#2)
  * [3. 静态库](#3)
  * [4. 动态库](#4)

<h3 id="1.1">OC调用swift</h3>

> 观察对应当前，oc文件的target
> targets->build settings->Product Module Name中设置的名字（这个名称很重要，swift 的头文件就是根据这个来命名的。）
> 在oc文件中，引用头文件：<productmodulename-swift.h>

1. 类继承
  * 报错：cannot subclass a class with objc_subclassing_restricted attribute

2. 类型兼容问题（类继承本质是，类型兼容问题？）

<h3 id="1.2">swift调用OC</h3>

> 创建oc源码文件的时候，可以顺着编译器提示，生成一个头文件<productname-bridging-header.h>
> 确认在target->build settings->Swift Compiler-Code Generation->Objective-C Bridging Header中设置了该桥接的头文件（当然该头文件可以自己创建，任意命名，并手动设置）
> 想要在swift类中调用的OC头文件，在上述桥接头文件中，一一import即可。

1. 类继承
  * swift中，可以继承oc类

2.
