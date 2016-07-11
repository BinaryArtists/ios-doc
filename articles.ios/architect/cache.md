## 缓存(未完成)

  * 主要考量：[tumblr/TMCache](https://github.com/tumblr/TMCache), [pinterest/PINCache](https://github.com/pinterest/PINCache), [ibireme/YYCache](https://github.com/ibireme/YYCache), [thisandagain/storage](https://github.com/thisandagain/storage)
  * NSURLCache
    * [设计一个移动应用的本地缓存机制](http://www.cnblogs.com/zhuqil/archive/2011/08/02/2124870.html)

todo：
以YYCache为目标做性能优化：[YYCache源码分析(一)](http://www.cocoachina.com/ios/20160614/16672.html), [YYCache 源码分析(二)](http://ios.jobbole.com/85740/), [YYCache 源码分析(三)](http://ios.jobbole.com/85940/)

### 系统方案

  1. NSUserDefaults
  2. Sqlite
  3. Json serialization
  4. NSArchiver
  5. CoreData
  6. 其他：
  6.1 [nicklockwood/AutoCoding](https://github.com/nicklockwood/AutoCoding)，自动归档，不需要写encodeWithCoder:、initWithCoder:。
  6.2 [nicklockwood/FastCoding](https://github.com/nicklockwood/FastCoding)，更快、可伸缩的二进制文件格式存储，替代归档、属性列表、json。

### Features

  1. 独立的 GCD 队列
  2. block 回调
  3. MD5
  4. TTL（Time to live） 时效性

### 文件缓存设计

  1. 功能设计
    * 存储数据到本地磁盘
    * 判断资源是否被缓存
    * 判断资源什么时候过期
  2. 类设计（主要参考storage）
    * _Cache_
    * _MemoryCache_
    * _DiskCache_
    * _CacheManager_
    * NSObject + 便利性 类别
  3. 特性设计（主要参考YYCache）
    * 内存、磁盘两级缓存
    * 支持缓存遍历，存储量管理

### 请求缓存设计

待 补充

### TMCache
官方说：Fast, non-deadlocking parallel object cache

  1. TMCache
    * 用dispatch_group_t实现异步并行处理，用信号量dispatch_semaphore_t实现同步处理。
  2. TMDiskCache
    * 允许增删改查的progress accessory
  3. TMMemoryCache
    * 允许增删改查的progress accessory
  4. TMTCacheBackgroundTaskManager
    * 后台任务处理

### PINCache

  1. 下标访问
  2. 信号量同步，改为针对同步，并共用
  3. 添加“判断是否存在某持久化对象方法”、“ttlCache行为”
  4. 修改“decodedString:” “encodeString:”方法的实现

### YYCache [YYCache 设计思路](http://blog.ibireme.com/2015/10/26/yycache/)
  1. 功能角度可以稍稍放低，重点看它怎么提升性能的！！！

  看几篇文章吧，对我这个菜鸟，启发很大：
  2. [学习YYCache总结](http://www.jianshu.com/p/45a974fafa7c)
    * YYMemoryCache实现 之 CFMutableDictionaryRef：CFDictionaryAddValue, If key which matches this key is already present in the dictionary, function does nothing ("add if absent")
    * YYMemoryCache实现 之 OSSpinLockLock：测试NSLock，pthread_mutex_t，OSSpinLock，@synchronized加锁的效率
      > [不再安全的 OSSpinLock](http://blog.ibireme.com/2016/01/16/spinlock_is_unsafe_in_ios/):除了 OSSpinLock 外，dispatch_semaphore 和 pthread_mutex 性能是最高的。有消息称，苹果在新系统中已经优化了 pthread_mutex 的性能，所以它看上去和 OSSpinLock 差距并没有那么大了。

    * YYMemoryCache实现 之 YYLinkedMapNode，YYLinkedMap：
    * YYKVStorage 离线存储：实现技术大致分为三类：基于文件读写、基于 mmap 文件内存映射、基于数据库。
      > TMDiskCache, PINDiskCache, SDWebImage 等缓存，都是基于文件系统的。
      > FastImageCache 采用的是 mmap 将文件映射到内存。
      > NSURLCache、FBDiskCache 都是基于 SQLite 数据库的。
