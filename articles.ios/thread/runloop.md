## Runloop要点

### 参考链接
[抛砖引玉](http://www.jianshu.com/p/37ab0397fec7)

### 目录
*	[1. 前言](#1)
*	[2. runloop关键类型](#2)
*	[3. 理解runloop内部原理](#3)
*	[4. runloop使用点](#4)
*	[5. 从事件处理看runloop](#5)
*	[6. runloop应用场合](#6)

<h3 id="1">前言</h3>
来自“简书”：关于Runloop的资料，官方文档总是太过简练，网上资源说法也不太统一，只能从CFRunLoopRef开源代码着手，试着学习总结下。（NSRunloop是对CFRunloopRef的面向对象封装，但是不是线程安全）。

<h3 id="2">runloop关键类型</h3>
	CFRunLoopRef、CFRunLoopSourceRef、CFRunLoopObserverRef、CFRunLoopTimerRef
	
	
*	是什么？
*	.....
*	.....

<h3 id="3">理解runloop内部原理</h3>
*	与线程的关系

<h3 id="4">runloop使用点</h3>
*	访问runloop和mode
*	管理定时器
*	管理端口
*	启动runloop
*	调度和取消消息

<h3 id="5">从事件处理看runloop</h3>
*	界面刷新
*	事件响应
*	手势识别
*	GCD任务
*	timer
*	网络请求

<h3 id="6">runloop应用场合</h3>
*	滑动和图片刷新
*	常驻子线程，保持一直处理事件
