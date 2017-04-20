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