## 一篇文章，读懂Netty的高性能架构之道

## [一篇文章，读懂Netty的高性能架构之道](http://mp.weixin.qq.com/s?__biz=MzA5Nzc4OTA1Mw==&mid=2659597788&idx=1&sn=8b226411fdf8d3f803d08d0c067e4ac2#rd)

*简介*
  1. Netty是一个高性能、异步事件驱动的NIO框架，它提供了对TCP、UDP和文件传输的支持，作为一个异步NIO框架，Netty的所有IO操作都是异步非阻塞的，通过Future-Listener机制，用户可以方便的主动获取或者通过通知机制获得IO操作结果。

    * NIO框架：
    * Future-Listener机制：

*应用*
  2. Netty在互联网领域、大数据分布式计算领域、游戏行业、通信行业等获得了广泛的应用

*原理*
  3. Netty 采用了比较典型的三层网络架构进行设计

  4. IO模型

  5. 线程调度模型

  6. 序列化方式

  7. 可靠性、安全性、扩展性
