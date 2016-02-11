## iOS 知识总结
---

### 工程环境与项目构成
*	workspace、project、target
*	基于上一点的，配置注意
*	framework、static lib以及编译链接配置注意点
*	XCode调试（快捷键，调试技巧）
*	编译脚本，等等辅助脚本
*	沙盒呢？
*	app store使用注意（建立应用，证书、配置文件，发布流程）
*	系统架构图与知识点整理[可参考](http://www.kejixun.com/article/201308/17085.html)
*	版本控制工具git、sourcetree
*	持续集成工具jakens
*	xib与StoryBoard

### [系统框架层] CocoaTouch、Media
*	UIKit
	1. 原理性的，图层啊，控制链啊，事件链啊什么的
	2. 应用管理
	3. 图形与窗口
	4. 触摸事件处理
	5. 用户接口管理
	6. 用来表征标准系统视图和控件的对象
	7. 支持文本和web内容
	8. 通过URL schema与其他应用的集成
	9. 加速计
	10. 内建camera
	11. 用户图片库
	12. 设备名称与模式信息
	2. UITableView
	3. UIScrollView
*	自动布局
*	图像技术（Quartz\CoreAnimation\OpenGL ES）
*	音频技术（CoreAudioFamily:CoreAudio.framework,AudioUnit.framework,AudioToolbox.framework\OpenAL）
*	视频技术（MediaPlayer.framework）


### [系统框架层] Core OS、Core Services
Core Services是Core OS基础上提供的更丰富的功能。

*	线程（POSIX线程、NSOperationQueue、GCD、NSThread）与运行循环（Run Loops）
*	网络（BSD sockets、CFNetwork）
*	文件与I/O
*	DNS服务与DNS劫持
*	内存（混用CoreFoundation和Foundation objects，toll-free bridging）
*	驱动层面有什么么？
*	Address.framework
*	CoreFoundation.framework（C语言的接口集）［Collection数据类型、Bundles、字符串管理、日期时间管理、原始数据块管理、首选项管理、URL和Stream操作、线程、端口与socket通信］
*	CoreLocation.framework
*	Security.framework
*	Sqlite
*	Xml

### 开发场景细分（实用）
*	［推送］Apple Push Notification Services，推送服务的申请与服务器、客户端集成。。。。。使用第三方的又如何？极光等
*	［日志系统］
*	［错误处理］
*	［图片服务］
*	［Http Dns设计］提、反DNS劫持
*	［二维码］
*	［蓝牙控制］

### OC语言层面（[条目参照](http://www.ibeifeng.com/bf_techios.html?baidu#6d)）
*	runtime
*	kvc、kvo
*	block
*	memory 管理
*	protocol
*	extension\category
*	NSInvocation

### Swift语言层面
*	PlayRound
*	Type check和推断
*	类型别名
*

### 代码层


### 工具层


### 客户端架构谈

### 开源模块精选
	1. 解析器
		*	html解析器：[hpple](https://github.com/topfunky/hpple), [example](http://segmentfault.com/a/1190000003860297)
		*

	2. Menu
		* [Context-Menu](https://github.com/Yalantis/Context-Menu.iOS)
		*

### summary by 唐巧博客
