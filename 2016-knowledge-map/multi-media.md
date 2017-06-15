# 多媒体

## 信号编码与压缩



## 编解码



## 媒体格式／容器

* 3gp
* mp4
* mov
* rmvb
* 

## 流媒体协议

http://blog.csdn.net/tttyd/article/details/12032357/

* RTP,  Real-time Transport Protocol)是用于Internet上针对多媒体数据流的一种传输层协议。RTP协议详细说明了在互联网上传递音频和视频的标准数据包格式。RTP协议常用于流媒体系统（配合RTCP协议），视频会议和一键通（Push to Talk）系统（配合H.323或SIP），使它成为IP电话产业的技术基础。RTP协议和RTP控制协议RTCP一起使用，而且它是建立在UDP协议上的
* RTCP, 实时传输控制协议（Real-time Transport Control Protocol或RTP Control Protocol或简写RTCP）是实时传输协议（RTP）的一个姐妹协议。RTCP为RTP媒体流提供信道外（out-of-band）控制。RTCP本身并不传输数据，但和RTP一起协作将多媒体数据打包和发送。RTCP定期在流多媒体会话参加者之间传输控制数据。RTCP的主要功能是为RTP所提供的服务质量（Quality of Service）提供反馈
* SRTP & SRTCP, 安全实时传输协议（Secure Real-time Transport Protocol或SRTP）是在实时传输协议（Real-time Transport Protocol或RTP）基础上所定义的一个协议，旨在为单播和多播应用程序中的实时传输协议的数据提供加密、消息认证、完整性保证和重放保护。它是由David Oran（思科）和Rolf Blom（爱立信）开发的，并最早由IETF于2004年3月作为RFC3711发布
* RTSP, RTSP（Real Time Streaming Protocol）是用来控制声音或影像的多媒体串流协议，并允许同时多个串流需求控制，传输时所用的网络通讯协定并不在其定义的范围内，服务器端可以自行选择使用TCP或UDP来传送串流内容，它的语法和运作跟HTTP 1.1类似，但并不特别强调时间同步，所以比较能容忍网络延迟。
* RTSP 和RTP的关系
* SDP, 会话描述协议（SDP）为会话通知、会话邀请和其它形式的多媒体会话初始化等目的提供了多媒体会话描述
* RTMP(Real Time Messaging Protocol) 
* HLS(HTTP Live Streaming) 

---
协议比较
1. [三种主流流媒体协议比较](http://www.zhixing123.cn/computer/55326.html)


## 实现框架

github上有现成的开源实现，推流、美颜、水印、弹幕、点赞动画、滤镜、播放

### 跨平台框架
--- 
* [ffmpeg]()

### android & iOS系统框架
1. iOS
    * SystemSound Services
    * AVAudioPlayer
    * Audio Queue Services
    * OpenAL

    * AVplayer
    * MPMoviePlayerViewController
    * Video ToolBox / above 8.0, if under 8.0, then use x264 software-encode
    * 
2. Android

### android & iOS第三方框架

VideoCore+GPUImage+ijkplayer

---
* [GPUImage]()
* [ijkplayer](), bilibili 开源的 ijkplayer, 它基于 ffmpeg，支持 Android 和 iOS，视频流和本地视频的播放都很强大
* [云巴 的 SDK](https://yunba.io/), 需要处理聊天室和弹幕需要的业务支撑, 基于 MQTT，采用 Erlang/OTP架构设计的云巴实时通信云服务，是一个 Pub/Sub 模型的双向实时系统，通过透明传输，可为直播平台实现所有的实时消息传输
* [BarrageRenderer](), 这个库是由 unash 所写的一个弹幕渲染引擎，相比其他弹幕库更为好用，因此我们选用该第三方库
* [LiveVideoCoreSDK](), 采集端
* [LFLiveKit](), 推流端


---
* [openCV]()

### 云解决方案

* 阿里云音视频解决方案
* 七牛云
* 金山云
* 乐视云
* 腾讯云
* 斗鱼直播推流端

### 优化方案

* 提高首播时间
    - 收流服务器主动推送GOP(group of pictures), 边缘节点缓存GOP，播放端可以快速加载，减少回源延迟
* GOP丢帧，为解决延时，为什么会有延时，网络抖动、网络拥塞导致数据发送不出去，丢完之后所有时间戳都要修改，切记，要不客户端会卡一个GOP的时间，是由于PTS(Presentation Timestamp)和DTS的原因，或者波翻起修正DTS和PTS也行
* 音视频同步
* 服务质量Qos(Quality of Service)
* 如何丢包率20%还能保障稳定、流畅的直播体验
* 源站主备切换和断线重连
* 根据TCP拥塞窗口做智能调度，当拥塞窗口过大说明节点服务质量不佳，需要切换节点和故障排查
* 增加上行、下行带宽探测接口，当带宽不满足时降低视频质量，即降低码率
* 定时获取最优的推流、拉流链路IP，尽可能保证提供最好的服务
* 监控必须要，监控各个节点的Qos状态，来做整个平台的资源配置优化和调度
* 用户体验：流畅、不卡顿、不花屏、断线重连、丢包策略、首画加载速度、丰富的礼物系统，为了提高用户体验，可以在后台加载其他页面数据，但要在用户体验和内存优化方面找到平衡点。