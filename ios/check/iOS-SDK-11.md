# iOS 11 SDK 新特性

[开发者所需要知道的 iOS 11 SDK 新特性](http://www.cocoachina.com/ios/20170607/19457.html)

## 新增框架

* Core ML
* ARKit

## Xcode

* 编辑器
    - Xcode 9 中编辑器进行了重写，支持了对 Swift 代码的重构 (虽然还很基础)，将 VCS 提到了更重要的位置，并添加了 GitHub 集成，可以进行同局域网的无线部署和调试。
    - Xcode 9 中的索引系统

* 编译器 
    - 速度
* XCAssets
    - Named Color： 在 xcassets 里添加颜色，然后在代码或者 IB 中引用这个颜色

## 其他值得注意的

* 拖拽 
* FileProvider 和 FileProviderUI - 提供一套类似 Files app 的界面，让你可以获取用户设备上或者云端的文件
* 不再支持 32 位 app
* DeviceCheck - 每天要用广告 ID 追踪用户的开发者现在有了更好地选择
* PDFKit - 这是一个在 macOS 上已经长期存在的框架，但却在 iOS 上姗姗来迟。你可以使用这个框架显示和操作 pdf 文件。
* IdentityLookup - 可以自己开发一个 app extension 来拦截系统 SMS 和 MMS 的信息。
* Core NFC - 在 iPhone 7 和 iPhone 7 Plus 上提供基础的近场通讯读取功能。
* Auto Fill - 从 iCloud Keychain 中获取密码，然后自动填充的功能现在开放给第三方开发者