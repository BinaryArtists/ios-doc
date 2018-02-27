# 网络层设计案例

*Core Features*

1. 

*Ext Features* 

1. 限流
2. 防刷
3. Cache
4. Offline Operation
5. 断点续传
6. 单元化路由？
7. 安全模块，动态密钥

## NetworkCore 核心功能 



### 



## NetworkExt 扩展功能

### [前置] Threshold Filter 限流

### [前置] Cache Filter 读缓存

### [前置] Offline Operation Filter 离线操作


### [后置] 错误码映射 Error Code Mapping Filter

### [后置] 错误处理 Error Handler Filter

### [后置] 解析动态配置 Dynamic API

### [后置] 写缓存 Cache Filter


### [后置] UT打点



## NetworkUtility


## 请求链路

Threshold Filter -> Cache Before Filter -> Offline Operation Filter -> Login Filter -> Setup Request Filter -> UT Before Filter -> mtopsdk -> networksdk
    
netwrorksdk -> mtopsdk -> UT After Filter -> Cache After Filter -> Dynamic API Config Filter -> Error Handler Filter -> Error Code Mapping Filter

