# NSURLSession

## API详解

### 创建配置的三种方式

```
// 返回标准配置，与NSURLConnection的网络协议栈是一样的，具有相同的共享NSHTTPCookieStorage，共享NSURLCache和共享NSURLCredentialStorage。
+ (NSURLSessionConfiguration *)defaultSessionConfiguration;

// 返回一个预设配置，没有持久性存储的缓存，Cookie或证书。这对于实现像"秘密浏览"功能的功能来说，是很理想的。
+ (NSURLSessionConfiguration *)ephemeralSessionConfiguration;

// 独特之处在于，它会创建一个后台会话。后台会话不同于常规的，普通的会话，它甚至可以在应用程序挂起，退出，
  崩溃的情况下运行上传和下载任务。初始化时指定的标识符，被用于向任何可能在进程外恢复后台传输的守护进程提供上下文。
+ (NSURLSessionConfiguration *)backgroundSessionConfigurationWithIdentifier:(NSString *)identifier     NS_AVAILABLE(10_10, 8_0);
```

###




## 参考

1. [iOS网络-NSURLSessionConfiguration会话配置](https://www.cnblogs.com/liugengqun/p/5140296.html)