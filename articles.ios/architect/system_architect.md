## OSX/iOS 的系统架构


介绍一下 OSX/iOS 的系统架构。

![图1](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/system_architect_1.jpg)

苹果官方将整个系统大致划分为上述4个层次：
应用层, 包括用户能接触到的图形应用，例如 Spotlight、Aqua、SpringBoard 等。
应用框架层, 即开发人员接触到的 Cocoa 等框架。
核心框架层, 包括各种核心框架、OpenGL 等内容。
Darwin 即操作系统的核心，包括系统内核、驱动、Shell 等内容，这一层是开源的，其所有源码都可以在 opensource.apple.com 里找到。

我们在深入看一下 Darwin 这个核心的架构：

![图2](https://github.com/BinaryArtists/objective-c-style-guide/blob/master/articles.ios/imges/system_architect_2.jpg)

其中，在硬件层上面的三个组成部分：Mach、BSD、IOKit (还包括一些上面没标注的内容)，共同组成了 XNU 内核。
XNU 内核的内环被称作 Mach，其作为一个微内核，仅提供了诸如处理器调度、IPC (进程间通信)等非常少量的基础服务。
BSD 层可以看作围绕 Mach 层的一个外环，其提供了诸如进程管理、文件系统和网络等功能。
IOKit 层是为设备驱动提供了一个面向对象(C++)的一个框架。

Mach 本身提供的 API 非常有限，而且苹果也不鼓励使用 Mach 的 API，但是这些API非常基础，如果没有这些API的话，其他任何工作都无法实施。在 Mach 中，所有的东西都是通过自己的对象实现的，进程、线程和虚拟内存都被称为"对象"。和其他架构不同， Mach 的对象间不能直接调用，只能通过消息传递的方式实现对象间的通信。"消息"是 Mach 中最基础的概念，消息在两个端口 (port) 之间传递，这就是 Mach 的 IPC (进程间通信) 的核心。