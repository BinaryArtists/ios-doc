## 'has changed to invalid binary'


### 使用了或者占用了 私有方法

1. _UICreateCGImageFromIOSurface_， 去掉 reveal.framework

### iOS 10，未在Info.plist中声明权限

当前必不可少的有：NSPhotoLibraryUsageDescription，NSMicrophoneUsageDescription，NSCameraUsageDescription，还有两个位置的
