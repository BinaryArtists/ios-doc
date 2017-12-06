# iPhone X 适配

1. 官方文档[Application Icons and Launch Images for iOS](https://developer.apple.com/library/content/samplecode/Icons/Introduction/Intro.html#//apple_ref/doc/uid/DTS40010442-Intro-DontLinkElementID_2)
2. [iOS项目基础一 - 官方应用图标和启动图片的尺寸规范和相应的工程设置](http://www.jianshu.com/p/3e333c55b43c)

## 启动图 (LaunchImage)

### 格式

1125px × 2436px (375pt × 812pt @3x)

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

作为参照物，让view可以相对某个view的safeAreaLayoutGuide做布局，从而保证view能正常、安全地显



