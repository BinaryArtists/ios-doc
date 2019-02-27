# 10.3.1 的坑

## [新机首次安装 app，请求网络权限“是否允许使用数据”](http://www.cocoachina.com/ios/20161206/18304.html)

1. 机器：iOS10国行
2. 其他博文：[iOS10国行网络权限问题](http://blog.csdn.net/yan325098/article/details/53115914)

## [iOS 10 的适配问题](http://www.jianshu.com/p/f8151d556930)

在添加info中添加下面权限字段：
```
<key>NSPhotoLibraryAddUsageDescription</key>
<string>App需要您的同意,才能访问媒体资料库</string>
```
当向相册中保存图片时，需要添加这个字段，否则没有保存的权限会导致崩溃；添加权限之后，点击保存会出现弹框，点击“好”才能成功保存图片；