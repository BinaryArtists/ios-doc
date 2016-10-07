## 网络模块


### 系统方案

  1. CFNetwork？

### 第三方方案

  1. YTKNetwork
  2. RTNetwork
  3. MKNetworkKit

### Features

  1. Serialization / Deserialization
  1.1 json支持，以及json合法性检查
  2. 缓存设计（还没做）
  2.1 请求缓存
  2.2 应答缓存
  2.3 按时间、版本号缓存
  3. 服务器地址管理（还没完善）
  3.1 服务器地址环境切换（一般是双备份，针对服务稳定性，不针对ddos攻击）：攻击，down机，不能提供服务，要跳转新服务器）
  4. 断点续传（还没做）
  5. block、delegate、signal 回调方式（还没完善）
  6. 批量请求方式，BatchRequest；串行请求方式（用于互相依赖的接口），ChainRequest。（还没做）
  7. 支持url、request、response，预处理或后处理的filter（用于修改、添加参数、修改路径等等）（还没完善）
  8. 插件（在某些请求发起的时候，再界面上显示“正在加载”hud）
  9. 请求取消（可手动取消，也可以依赖调用者的dealloc取消）

### 概念抽象

  1. 可序列化model
  2. 序列化工具
  3. http client
  4. network 抽象
  5. netstatus 抽象
  6. netconfig 抽象
  7. url 缓存
    *     1. https://github.com/rs/SDURLCache
    *     2. AFURLCache
