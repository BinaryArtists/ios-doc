# 9 的坑

## [关于iOS9 Https的问题](http://www.jianshu.com/p/b671d2ee4458)

支持 https：
```
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true />
</dict>
```

不支持 https：
```
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>yourserver.com</key>
        <dict>
            <!-- Include to allow insecure HTTP requests -->
            <key>NSIncludesSubdomains</key>
            <true/>

            <!-- Include to specify minim TLS version -->
            <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSTemporaryExceptionMinimumTLSVersion</key>
            <string>TLSv1.1</string>
        </dict>
    </dict>
</dict>
```

注意：这个配置问题，会影响 iOS 9，iOS 10 的网络操作表现。。当时遇到问题时，忘记了。花了很久。

## 新的关键字：nonnull,nullable,null_resettable,_Null_unspecified

* iOS9新出的关键字:用来修饰属性,或者方法的参数,方法的返回值
* 好处
    - 迎合swift
    - 提高我们开发人员开发规范,减少程序员之间交流
* 简单介绍
    - nullable作用:表示可以为空
    - nonnull作用:不能为空
    - 在NS_ASSUME_NONNULL_BEGIN和NS_ASSUME_NONNULL_END之间,定义的所有对象属性和方法默认都是nonnull
    - null_resettable作用: get:不能返回为空, set可以为空
    ```
    // 书写方式:
    @property (nonatomic, strong, null_resettable) NSString *name;
    // 注意;如果使用null_resettable,必须 重写get方法或者set方法,处理传递的值为空的情况
    ```
    - _Null_unspecified:不确定是否为空
    ```
    书写方式只有这种
    方式一
    @property (nonatomic, strong) NSString *_Null_unspecified name;
    方式二
    @property (nonatomic, strong) NSString *__null_unspecified name;
    ```