# 提审 checkilst

## 打包

1. 图片
    * 确认2x, 3x图片的提供
    * 清理多余的图片
2. 第三方库
    * 环境配置
    * KEY设置
    *
3. 苹果硬性要求
    * [IPv6支持](https://github.com/BinaryArtists/not-just-code/blob/master/ios-check/ipv6.md)

## 提审

1. 应用截图检查
    * 图片内容检查, 图片须能体现应用的真实内容，不能存在与应用无关的内容
    * 第三方移动平台字样检查, 图片中不能存在android，winphone等第三方平台字样
    * 测试相关字样的检查, 图片中不能存在内测、测试、公测、体验、demo版、beta版、test版等字样
2. 基本要点
    * 如果需要网络连接，在没有网络的情况下要告知用户
    * 运行若干次之后不要过期或停止工作
    * 在每个输入区域使用相关的键盘（比如在输入手机号的地方要调出数字键盘）
    * 仅在连接WiFi的情况下才能播放视频或下载其它大型文件
    * 如需发送私人数据到服务器，需要告知用户，并提供取消选项
3. 提交应用程序文件时的细节
    * iTunes关键词不能包含其他应用的名称
    * 应用截屏不能包含错误状态，包括iAd的错误
    * 提供各种尺寸的图标：57，72 (iPad)，114，512
    * 版本号大于等于1.0
    * info.plist文件中所需要的设备功能和应用的实际需求相符
    * NSZombieEnabled设为NO
    * 如使用推送通知，需要在entitlements.plist中添加用于“aps-environment”授权信息
    * 若使用推送通知，你的应用ID必须已经激活推送通知功能
    * 在更新iTunes的“新功能”介绍时，内容要和应用实际变化相符
    * 更新后的应用版本号要大于上个版本
4. 杂项
    * 应用里不能存在已经作废的和未来版本发布有关的按钮和功能
    * 作为应用的一部分，应用加载的网页必须是线上的，并且可用
    * 任何相关的email地址都必须存在并且可用
    * 所有应用升级和其他交易都必须通过苹果应用商店完成
    * 应用不能是一个简单的浏览器窗口中的的网站
    * 若使用加密，必须已经通过BIS注册，并能提供文档
    * 如果你的应用需要登录，需要提供测试账户
    * 如果应用使用MapKit，不得隐藏或掩饰Google Logo / 高德 Logo
    * 如果使用摄像头，不得关闭快门声音
5. 被拒绝敏感区
    * 应用里面带检测更新按钮
    * Info.Plist中的注意点
        > NSCalendarsUsageDescription
        > NSPhotoLibraryUsageDescription
        > NSBluetoothPeripheralUsageDescription
        > NSMicrophoneUsageDescription
        > NSMotionUsageDescription
        > Missing Push Notification Entitlement (如果创建App的时候，选择了推送服务）
