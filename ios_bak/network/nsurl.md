## NSURL

### [NSURL基本操作](http://blog.csdn.net/zhibudefeng/article/details/7920686)

直接看代码：
```
    NSURL *url = [NSURL URLWithString:@"http://www.baidu.com/s?tn=baiduhome_pg&bs=NSRUL&f=8&rsv_bp=1&rsv_spt=1&wd=NSurl&inputT=2709"];  

    NSLog(@"Scheme: %@", [url scheme]);  

    NSLog(@"Host: %@", [url host]);  

    NSLog(@"Port: %@", [url port]);  

    NSLog(@"Path: %@", [url path]);  

    NSLog(@"Relative path: %@", [url relativePath]);  

    NSLog(@"Path components as array: %@", [url pathComponents]);  

    NSLog(@"Parameter string: %@", [url parameterString]);  

    NSLog(@"Query: %@", [url query]);  

    NSLog(@"Fragment: %@", [url fragment]);  

    NSLog(@"User: %@", [url user]);  

    NSLog(@"Password: %@", [url password]);
```

结果：
```
	2012-08-29 15:52:23.781 NSurl[3560:f803] Scheme: http  
	2012-08-29 15:52:32.793 NSurl[3560:f803] Host: www.baidu.com  
	2012-08-29 15:52:39.102 NSurl[3560:f803] Port: (null)  
	2012-08-29 15:52:42.590 NSurl[3560:f803] Path: /s  
	2012-08-29 15:52:52.516 NSurl[3560:f803] Relative path: /s  
	2012-08-29 15:53:05.576 NSurl[3560:f803] Path components as array: (  
	    "/",  
	    s  
	)  
	2012-08-29 15:53:32.861 NSurl[3560:f803] Parameter string: (null)  
	2012-08-29 15:53:37.528 NSurl[3560:f803] Query: tn=baiduhome_pg&bs=NSRUL&f=8&rsv_bp=1&rsv_spt=1&wd=NSurl&inputT=2709  
	2012-08-29 15:53:52.942 NSurl[3560:f803] Fragment: (null)  
	2012-08-29 15:53:54.539 NSurl[3560:f803] User: (null)  
	2012-08-29 15:53:57.808 NSurl[3560:f803] Password: (null)  
```

### [完美替代 NSMutableURL:NSURLComponents](http://nshipster.cn/nsurl/)

另外还可以参考：[NSURL/NSURLComponents](http://www.cocoachina.com/industry/20140414/8155.html)

苹果在 iOS 7 和 OS X Mavericks 中悄悄添加了 NSURLComponents，这样就可以完美替代 NSMutableURL 了。但文档还不是很完善，所以这个类仍然是近期Foundation新增类中隐晦的一块。

创建 NSURLComponents 实例和创建 NSURL 实例的方法差不多，通过一个 NSString 和一个非必需的base URL参数创建(+componentsWithString: & +componentsWithURL:resolvingAgainstBaseURL:)。也可以用 alloc init 创建一个空的容器，和 NSDateComponents 差不多。

NSURL 和 NSURLComponents 的不同之处在于，URL component 属性是 readwrite 的。它提供了安全直接的方法来修改URL的各个部分：

scheme
user
password
host
port
path
query
fragment

如果尝试赋值一个非法的scheme或port，会抛出一个异常。

另外，NSURLComponents 也有 readwrite 属性对每个 component 进行 [percent-encoded]。

percentEncodedUser
percentEncodedPassword
percentEncodedHost
percentEncodedPath
percentEncodedQuery
percentEncodedFragment

对这些 percent encoding 属性的 get 操作可能会造成 retain 增加。set 操作会默认认为该 component 已经正确 encode 了。试图赋值一个非法的 percent encode 值会抛出异常。虽然 ';' 是一个合法的路径字符，但建议还是percent-encoded一下来兼容NSURL（传递给-stringByAddingPercentEncodingWithAllowedCharacters: URLPathAllowedCharacterSet参数 会将所有的 ';' 字符 percent-encode）。
