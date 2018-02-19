# iPhone X 适配

[iPhone X的简单适配](http://www.cocoachina.com/ios/20170917/20590.html)

iOS 11 设备上运行出现最多问题应该就是 tableView 莫名奇妙地偏移了 20pt 或者 64pt。原因是 iOS 11 弃用了 automaticallyAdjustsScrollViewInsets 属性，取而代之的是 UIScrollView 新增了 contentInsetAdjustmentBehavior 属性，这一切的罪魁祸首都是新引入的 Safe Area。

Safe Area 帮助我们将 view 放置在整个屏幕的可视的部分。即使把 navigationBar 设置为透明，系统也认为安全区域是从 navigationBar 的 bottom 开始的。

安全区域定义了 view 中可视区域的部分，保证不被系统的状态栏、或父视图提供的 view 如导航栏覆盖。可以使用 additionalSafeAreaInsets 去扩展安全区域。每个 view 都可以改变安全区域嵌入的大小，controller 也可以。

safeAreaInsets 属性反映了一个 view 距离该 view 的安全区域的边距。对于一个 controller 的 rootView 而言，safeAreaInsets 值包括了被 statusBar 和其他可视的bars 覆盖的区域和其他通过 additionalSafeAreaInsets 自定义的 insets 值。对于 view 层次中的其他 view，safeAreaInsets 值反映了 view 被覆盖的部分。如果一个 view 全部在它父视图的安全区域内，则 safeAreaInsets 值为(0,0,0,0)。

## 数值

1. 刘海高度：30
2. 屏幕分辨率 1125 x 2436px（375pt×812pt @3x）

## 状态栏

iPhone X状态条由20px变成了44px，UITabBar由49px变成了83px。设置布局时y直接写成64的就要根据机型设置。可以设置宏
```
#define Device_Is_iPhoneX ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(1125, 2436), [[UIScreen mainScreen] currentMode].size) : NO)，
```

## 控制器生命周期之viewSafeAreaInsetsDidChange

viewDidLoad
viewWillAppear
viewSafeAreaInsetsDidChange（NEW）
viewWillLayoutSubviews
viewDidAppear

可以在 viewSafeAreaInsetsDidChange 后，设置 Safe Area
```
if #available(iOS 11.0, *) {
   self.additionalSafeAreaInsets = UIEdgeInsetsMake(10, 0, 0, 34)
}
```

## iPhone X - iOS 11

这里总结了大家iOS 11，iPhone X 适配问题
-------------------------------------

iOS 11适配
---------

[简书App适配iOS 11](http://www.jianshu.com/p/26fc39135c34)  
 
[掘金客户端适配iOS11简单记录](https://juejin.im/entry/59bb92ab6fb9a00a681ac051)
 
[iOS 11 安全区域适配总结](http://mp.weixin.qq.com/s/W1_0VrchCO50owhJNmJnuQ)
 
[iOS开发-适配iOS 11](http://www.jianshu.com/p/a356b2ed4ceb)

[适配iOS11&iPhoneX的一些坑 ](https://mp.weixin.qq.com/s?__biz=MjM5OTM0MzIwMQ==&amp;mid=2652552818&amp;idx=1&amp;sn=69db895d4d4078bd83e7e1655fcdd5f1&amp;chksm=bcd2fb7c8ba5726a0d7481ca5960ec3c1600b214f15841542a41c58612ef4866a9551cd82640#rd)
 
[完美适配 iOS11 导航栏](http://www.cocoachina.com/ios/20170919/20597.html)

[腾讯WeTest 你可能需要为你的APP适配iOS11](http://wetest.qq.com/lab/view/326.html)

[iOS 11 适配看这篇还不够？](http://www.jishux.com/plus/view-606748-1.html)

iPhone X 适配
------------

[三分钟了解iPhone X设计规范（详细）](http://www.gamfe.com/news/201709/sheji-10451.html)

[饿了么 UED iPhone X设计适配指南 & iOS 11新特性](https://zhuanlan.zhihu.com/p/29327102)

[iPhone X 适配](http://www.jianshu.com/p/9796cd3f180e)

[iPhone X 适配指南 (官方版)](https://developer.apple.com/cn/ios/update-apps-for-iphone-x/)

[美团iPhone X 刘海打理指北](https://tech.meituan.com/iPhoneX%E5%88%98%E6%B5%B7%E6%89%93%E7%90%86%E6%8C%87%E5%8C%97.html)


2017.9.21 增加iPhone X 上TabBar拉伸的,压缩的问题
------------------------------------
如果是那种自定义TabBar中间是按钮的这种操作，我试了一下，之前中间按钮的高度设置的是TabBar的高度（ CGFloat buttonH = self.frame.size.height;），现在直接赋值，也可以解决这种问题 。CGFloat buttonH = 49;

[TabBar被拉伸的解决方法](https://stackoverflow.com/questions/46214740/ios-11-iphone-x-simulator-uitabbar-icons-and-titles-being-rendered-on-top-coveri)

[ios 11 UITabBar UITabBarItem positioning issue](https://stackoverflow.com/questions/44822558/ios-11-uitabbar-uitabbaritem-positioning-issue)

[奇点在 iPhone X 的 break](https://imtx.me/archives/2374.html)

[iPhone X在push的时候tabbar会上移是怎么回事？](http://www.cocoachina.com/bbs/read.php?tid=1726043)，结论：系统问题，解决方案：[iPhoneX的带有TabBar的画面跳转时，位置跳动的问题](http://blog.csdn.net/SVALBARDKSY/article/details/78294352?locationNum=10&fps=1)

[Change UITabBar height](https://stackoverflow.com/questions/23044218/change-uitabbar-height), 我遇到的问题是：视图的切换，隐藏、显示tabbar等操作后，在viewWillAppear方法中，tabbar的frame变成了{{0, 763}, {375, 49}}，应该是 {{0, 729}, {375, 83}}

 2017.9.28 增加界面底部不带TabBar，iPhone X适配的问题
 -------------------------------------------
 
 今天有小伙伴询问了界面底部不带TabBar，iPhone X底部圆角导致，界面不美观，怎么解决。
 
 目前的解决办法：
 
 1.tableView在初始化的时候，Height增加一个宏，判断一下，如果是iPhone X 底部留，20px。
 
 2.保持view的backgroundColor跟tableView一致。
 
     #define IS_IPHONEX (([[UIScreen mainScreen] bounds].size.height-812)?NO:YES)
 
     #define Height (IS_IPHONEX ? ([[UIScreen mainScreen] bounds].size.height-20):([[UIScreen mainScreen] bounds].size.height))
 

iOS 11获取设备名称
-----------------

    if ([platform isEqualToString:@"iPhone10,1"]) return @"iPhone 8";
    if ([platform isEqualToString:@"iPhone10,4"]) return @"iPhone 8";
    if ([platform isEqualToString:@"iPhone10,2"]) return @"iPhone 8 Plus";
    if ([platform isEqualToString:@"iPhone10,5"]) return @"iPhone 8 Plus";
    if ([platform isEqualToString:@"iPhone10,3"]) return @"iPhone X";
    if ([platform isEqualToString:@"iPhone10,6"]) return @"iPhone X";

