# iPhone X 适配

1. 官方文档[Application Icons and Launch Images for iOS](https://developer.apple.com/library/content/samplecode/Icons/Introduction/Intro.html#//apple_ref/doc/uid/DTS40010442-Intro-DontLinkElementID_2)
2. [iOS项目基础 - 官方应用图标和启动图片的尺寸规范和相应的工程设置](http://www.jianshu.com/p/3e333c55b43c)
3. [iPhone X 适配手Q H5 页面通用解决方案](https://cloud.tencent.com/community/article/686372)

## 强势典范：

1. [iPhoneX 适配实践](https://cloud.tencent.com/community/article/322940)
2. [Human Interface Guidelines iOS](https://developer.apple.com/ios/human-interface-guidelines/overview/iphone-x/)

## 启动图 (LaunchImage)

### 规格：

1125px × 2436px (375pt × 812pt @3x)

竖屏规格：1125px × 2436px (375pt × 812pt @3x)
横屏规格：2436px × 1125px (812pt × 375pt @3x)

### 状态栏

高度增加了24像素，来电或者热点不会导致状态栏高度变化（iPhone X 以外的机型，在来电、热点出现时，会导致状态栏高度变化？）

### 底部栏

TabBar高度增加了34像素

```
UITabBar: 0x7f94ca71a7b0; frame = (0 729; 375 83);
```

ToolBar高度不变，只是向上偏移了34像素
```
UIToolbar: 0x7f89c7c0b9e0; frame = (0 730; 375 48);
```

### SafeArea安全区域

#### 设计原则

1. 所有设计的内容（不包括滚动列表）不要被屏幕圆角、上方传感器区域和下方home键指示器区域遮挡。

2. 提供全屏用户体验、这里主要是指列表要延展到屏幕底部

3. 避免将可交互控件放在屏幕底部或者屏幕圆角区域，防止视觉遮挡和系统边缘手势冲突 (屏幕角落边缘可交互按钮需要在SafeArea内部)

4. 不要刻意遮挡和和引导屏幕的关键位置，比如用纯黑色的navigationbar和toolbar遮住上下区域，或者用闪亮的背景强调底部指示器区域。

### 命名

Default-iOS11-812h@3x.png

### LaunchImage方式

1. 创建【LaunchImage】集合
2. 把启动图片添加到工程 Images.xcassets 文件夹下的中的【LaunchImage】中

### 文件拖入方式

1. 添加iPhone X的启动图片，把图片拖进文件夹后，需要在info.plist文件的 UILaunchImages中添加Item，具体添加内如如下：
```
UILaunchImages      						Array			(6 items)
	- Item 0           						Dictionary		(4 items)
		> UILaunchImageOrientation  		String			Protrait
		> UILaunchImageName  				String			Default-iOS11-812h
		> UILaunchImageSize  				String			{312, 812}
		> UILaunchImageMinimumOSVersion 	String			11.0
	- Item 1								Dictionary		(4 items)
```

### LanunchScreen.xib方式

弃用

## 欢迎页的图

做尺寸适配，和启动图蕾丝

## Safe Area

### 竖屏

```
UIEdgeInsets
  - top : 44.0
  - left : 0.0
  - bottom : 34.0
  - right : 0.0
```

### 横屏

```
  - top : 0.0
  - left : 44.0
  - bottom : 21.0
  - right : 44.0
```

## 自动布局

* iOS11 以前，我们布局时，视图的 top 和 bottom 一般参照的是 Top Layout Guide 和 Bottom Layout Guide
* iOS11 以后，那两个参照已经 deprecated (过时)了，而被 Safe Area 取代。
* Safe Area 要求最低支持 iOS9.0 
	但凡和父视图相关的 top 或者 bottom 属性的自动布局，应该参照 Safe Area。（之前默认是 Superview，当然如果产品允许，那就是另一回事了）

## WKWebViewJavascriptBridge

```
// 原贴地址：https://github.com/marcuswestin/WebViewJavascriptBridge/issues/278
- (void)webView:(WKWebView *)webView
didFailNavigation:(WKNavigation *)navigation
      withError:(NSError *)error {
    // ……
    // Xcode 升级到9后 WKWebViewJavascriptBridge.m 中有个 bug
    // 需要在"decisionHandler(WKNavigationActionPolicyCancel);"后加上 return，否则崩溃。
    // 不升级 Xcode 的话，没问题。
    decisionHandler(WKNavigationActionPolicyCancel);
    return;
}
```

## 底部有按钮的页面

### 控制器生命周期之viewSafeAreaInsetsDidChange

```
if #available(iOS 11.0, *) {
   self.additionalSafeAreaInsets = UIEdgeInsetsMake(10, 0, 0, 34)
}
```

## 顶部状态栏

### safeAreaLayoutGuide

作为参照物，让view可以相对某个view的safeAreaLayoutGuide做布局，从而保证view能正常、安全地显示



