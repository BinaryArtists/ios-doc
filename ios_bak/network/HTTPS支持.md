## https support

苹果在iOS 9中首次引入了ATS安全传输功能，当应用请求联网的时候，该功能将强制应用使用HTTPS连接而非一般的 HTTP。启用HTTPS网络连接之后，数据传输的安全性将大幅提升，不容易被黑客拦截破译。ATS在许多银行和通信应用中具有很重要的作用，尽管当前ATS还只是一个可选功能。

### Basics summary

1、2017年1月1日起，苹果App Store中的所有App都必须启用 App Transport Security(ATS)安全功能。
2、App Transport Security(应用程序安全传输)，简称 ATS，是苹果在 iOS 9 中首次推出的一项隐私安全保护功能，启用ATS后，它会屏蔽明文HTTP资源加载，强制App通过HTTPS连接网络服务，通过传输加密保障用户数据安全。
3、ATS要求服务器必须支持传输层安全(TLS)协议1.2以上版本;证书必须使用SHA256或更高的哈希算法签名;必须使用2048位以上RSA密钥或256位以上ECC算法等等，不满足条件的证书，ATS都会拒绝连接。强制开启ATS体现了苹果一贯的隐私保护态度。
开发者需要HTTPS证书，可以到沃通CA申请，沃通CA还推出了免费的https证书，兼容苹果所有设备。

### Client - [iOS9苹果将原http协议改成了https协议的方法](http://www.jb51.net/article/77717.htm)

解决方法：
在info.plist 加入key
```
<key>NSAppTransportSecurity</key>
<dict>
<key>NSAllowsArbitraryLoads</key>
<true/>
</dict>
```

下面给大家介绍ios中http 和https 协议的访问
最近做个项目，开始采用的是HTTP协议实现客户端和服务器端的交互，后来需要改成HTTPS协议。在修改的过程中发现了一些问题，解决方案如下：

	* HTTP：
```
NSString *urlString =[NSString stringWithFormat:@"https://127.0.0.1/default.aspx?USER=%@",@"111"];
NSMutableURLRequest *request = [[[NSMutableURLRequest alloc] init] autorelease];
[request setURL:[NSURL URLWithString:urlString]];
[request setHTTPMethod:@"GET"];
NSHTTPURLResponse* urlResponse = nil;
NSError *error = [[NSError alloc] init];
NSData *responseData = [NSURLConnection sendSynchronousRequest:request returningResponse:&urlResponse error:&error];
NSMutableString *result = [[NSMutableString alloc] initWithData:responseData encoding:NSUTF8StringEncoding];
NSLog(@"The result string is :%@",result);


	* HTTPS
事件触发
```
{ 
NSString *urlString =[NSString stringWithFormat:@"https://127.0.0.1/default.aspx?USER=%@",@"111"];
NSMutableURLRequest *request = [[NSMutableURLRequest alloc] initWithURL:[NSURL URLWithString:urlString] cachePolicy:NSURLRequestReloadIgnoringLocalCacheData timeoutInterval:5];
//设置请求方式为get
[request setHTTPMethod:@"GET"];
//添加用户会话id
[request addValue:@"text/html" forHTTPHeaderField:@"Content-Type"];
//连接发送请求
finished = false;
NSURLConnection *conn = [[NSURLConnection alloc] initWithRequest:request delegate:self];
//堵塞线程，等待结束
while(!finished) {
	[[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
}
} 
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse*)response 
{}
- (void)connectionDidFinishLoading:(NSURLConnection *)connection 
{
	//[_waitingDialog dismissWithClickedButtonIndex:0 animated:NO];
	[connection release];
}
-(void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error
{ 
}
- (BOOL)connectionShouldUseCredentialStorage:(NSURLConnection *)connection{
	return NO;
}
//下面两段是重点，要服务器端单项HTTPS 验证，iOS 客户端忽略证书验证。
- (BOOL)connection:(NSURLConnection *)connection canAuthenticateAgainstProtectionSpace:(NSURLProtectionSpace *)protectionSpace {
	return [protectionSpace.authenticationMethod isEqualToString:NSURLAuthenticationMethodServerTrust];
} 
- (void)connection:(NSURLConnection *)connection didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge { 
	NSLog(@"didReceiveAuthenticationChallenge %@ %zd", [[challenge protectionSpace] authenticationMethod], (ssize_t) [challenge previousFailureCount]);
	if ([challenge.protectionSpace.authenticationMethod isEqualToString:NSURLAuthenticationMethodServerTrust]){
		[[challenge sender] useCredential:[NSURLCredential credentialForTrust:challenge.protectionSpace.serverTrust] forAuthenticationChallenge:challenge];
		[[challenge sender] continueWithoutCredentialForAuthenticationChallenge: challenge];
	}
} 
NSLog(@"get the whole response");
	//[receivedData setLength:0];
}
//处理数据 
- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data
{ 
}
```

### Server - ....