## iOS 10 适配

主要涉及：推送、HTTPS、隐私权限、状态栏、CollectionView性能优化、UIcolor、字体随系统改变等

### Xcode8代码出现ubsystem: com.apple.UIKit, category: HIDEventFiltered, enable_level: 0, persist_level: 0, default_ttl: 0, info_ttl: 0, debug_ttl: 0, generate_symptoms: 0, enable_oversize: 1, privacy_setting: 2, enable_private_data: 0 2016-09-14 17:18:55.843428 MyApp[6880:340837]

解决办法：【product】-【scheme】-【Edit Scheme】-【Run】-【Argument】-【Environment Variable】添加keyValue【OS_ACTIVITY_MODE   disable】可以停止输出打印此日志

遗留问题：还会出现Class PLBuildVersion is implemented in both /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator.sdk/System/Library/PrivateFrameworks/AssetsLibraryServices.framework/AssetsLibraryServices (0x10a39a910) and /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator.sdk/System/Library/PrivateFrameworks/PhotoLibraryServices.framework/PhotoLibraryServices (0x10a1c4210). One of the two will be used. Which one is undefined.


### MJRefresh出现崩溃现象

分析：获取设备语言问题

解决办法：类库增加判断
```
if (range.location != NSNotFound) {  
            language = [language substringToIndex:range.location];  
}  
```

### Xcode8取消了这一快捷键

终端下执行命令sudo /usr/libexec/xpccachectl，然后重启即可

### [4] StroyBoard问题
	<1>Xcode 打开SB频繁出现崩溃现象
	<2>Xcode 打开SB后 约束出现大量警告⚠️

### NSLog打印问题

[plain] view plain copy 在CODE上查看代码片派生到我的代码片
[] -[NWConcrete_tcp_connection dealloc] 11    
[] nw_socket_handle_socket_event Event mask: 0x4    
[] nw_socket_handle_socket_event Socket received WRITE_CLOSE event    
[] nw_socket_handle_socket_event Event mask: 0x4    
[] nw_socket_handle_socket_event Socket received WRITE_CLOSE event] tcp_connection_cancel 5    
[] nw_socket_handle_socket_event Event mask: 0x4] nw_socket_handle_socket_event Socket received WRITE_CLOSE event    
[] nw_endpoint_handler_cancel [5 192.168.2.51:1999 ready proxy (satisfied)]] -[NWConcrete_nw_endpoint_proxy cancelWithHandler:forced:] [5 192.168.2.51:1999 cancelled proxy (null)]] nw_endpoint_handler_cancel [5.1 192.168.2.51:1999 ready socket-flow (satisfied)]    
[] nw_endpoint_flow_protocol_disconnected [5.1 192.168.2.51:1999 cancelled socket-flow (null)] Output protocol disconnected    
[] nw_endpoint_handler_cancel [5.2 192.168.2.51:1999 initial path (null)]    
[] -[NWConcrete_tcp_connection dealloc] 5    


解决方法1:
 在文件开头加入 #ifdef DEBUG #define NSLog(FORMAT, ...) fprintf(stderr,"%s\n",[[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]); #else #define NSLog(...) #endif 即可。

### iOS 更新内容详解
1 . 界面方面的修改（更加像安卓）
苹果为iOS 10带来了全新的通知查看功能，当我们拿起手机的时候，手机会自动亮屏并且在锁屏界面上显示推送的消息，不用按电源或者Home键就能点亮，十分方便。
2 .  锁屏界面上面的消息功能也更加强大了，可以不解锁的情况下直接通过3D Touch进行操作，包括删除、回复短信等等。iOS 10在UI设计上的改动并不多，基本上是在延续扁平化设计的基础上，新增了一些卡片式的元素——包括上滑控制中心、下拉通知列表、Spotlight界面等，使得整个UI的界面风格更加和谐统一。
3.   识别ip ，比如识别是否是骚扰电话的功能
     电话IP自动识别肯定是最显眼的一招。在安卓上面已经出现蛮久的这个功能，这次iOS 10终于加入了。目前调用的是国内某互联网公司的数据库，如果对方是中介、快递、骚扰电话等等都能提前知道，更加方便和安全了。
4.  苹果自己地图增加了 滴滴搭车功能
5.  iMessage 的改变
    iMessage增加了很多表情符号，比以往也大了3倍。在对话当中随意可以插入图片、音乐、甚至是手绘的图案，还有会出现类似QQ，微信的节日动画，生日动画等动态动画效果。这些手绘的图案在对方手机上同样可以看到笔迹动画展示的。  
6. 3D Touch的更新
   下拉通知界面，可通过轻压通知进入更详细的信息面板，同时还允许用户进行快速回复。除了文本之外，图片和视频一样可以通过3D Touch功能实现快速预览和回复。
7. Siri 功能的增加
    现在的Siri，已经可以直接调用不少第三方软件了，比如微信、支付宝、Uber、滴滴等软件。譬如我们呼出Siri之后，只需要说一句“帮我转账多少钱给某人”，Siri就会弹出一个支付宝的页面，里面是转账前的概览，然后会询问你是不是确定转载，只需要说“确定”，就完成了转账。
8. 好消息呀， 可以删除部分原生应用了
9. 相册功能的升级
     iOS 10针对照片应用进行了大幅度的优化，智能地将照片进行分类，并可直接通过搜索关键字进行查找。
　 例如，通过关键字“车”进行搜索，iOS会将自动识别与“车”相关的照片，并直接筛选出来。于此同时，相簿中新增了自动分类功能，可以对人的脸部进行识别，然后自动归类等。

### 其他问题

    * [iOS10相册相机闪退bug](http://www.jianshu.com/p/5085430b029f)

    * [iOS 10 因苹果健康导致闪退 crash](http://www.jianshu.com/p/545bd1bf5a23)

    * [ios10相机等崩溃](http://www.jianshu.com/p/ec15dadd38f3)

    * [iOS10 配置须知](http://www.jianshu.com/p/65f21dc5c556)

    * [iOS开发 适配iOS10以及Xcode8](http://www.jianshu.com/p/9756992a35ca)

    * [iOS 10 的适配问题](http://www.jianshu.com/p/f8151d556930)

    * [SpringBoard was unable to service the request.](http://www.jianshu.com/p/14fce940346a)

### 参考：
	* 1. [iOS10的错误解决办法](http://blog.csdn.net/zhao15127334470/article/details/52623944)
	* 2. [iOS10和Xcode8适配](http://blog.zlcode.com/2016/09/25/ios10-adaptation/)
    * 3. [玩转 iOS 10 推送 —— UserNotifications Framework（上）](http://www.jianshu.com/p/2f3202b5e758)
    * 4. [兼容iOS 10 资料整理笔记](http://www.jianshu.com/p/0cc7aad638d9)