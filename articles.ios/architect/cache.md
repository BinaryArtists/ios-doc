## 缓存(未完成)

  * 主要考量：[tumblr/TMCache](https://github.com/tumblr/TMCache), [pinterest/PINCache](https://github.com/pinterest/PINCache), [ibireme/YYCache](https://github.com/ibireme/YYCache), [thisandagain/storage](https://github.com/thisandagain/storage)
  * NSURLCache
    * [设计一个移动应用的本地缓存机制](http://www.cnblogs.com/zhuqil/archive/2011/08/02/2124870.html)

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

  1. TMCache
    * 用dispatch_group_t实现异步并行处理，用信号量dispatch_semaphore_t实现同步处理。
  2. TMDiskCache
    * 允许增删改查的progress accessory
  3. TMMemoryCache
    * 允许增删改查的progress accessory
  4. TMTCacheBackgroundTaskManager
    *

### PINCache



### YYCache


### 性能

还没计算
