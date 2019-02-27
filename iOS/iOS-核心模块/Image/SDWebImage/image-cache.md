# SDWebImage的缓存更新机制

### [SDWebImage使用SDWebImageRefreshCached后，图片没有刷新的问题讨论](http://blog.csdn.net/jiadabin/article/details/52129789)

1. 让服务器更新url，也就是说服务器端如果更新了头像，那么就生成新的url
2. 让服务器端支持cache-control，由于小弟对服务器端开发知识有限，个人觉得这个策略可能无法满足客户端的需求，还请各位大神斧正
3. 修改SDWebImage，让它支持http的Last-Modified或者etag（前提是服务器端也要支持）

### [SDWebImage支持URL不变时更新图片内容](http://www.jianshu.com/p/3d5a99790760)

通过查看SDWebImageDownloader的源码得知，它开放了一个headersFilter的block，意在让开发者可以对所有图片请求追加一些额外的header
```
SDWebImageDownloader *imgDownloader = SDWebImageManager.sharedManager.imageDownloader;  
imgDownloader.headersFilter  = ^NSDictionary *(NSURL *url, NSDictionary *headers) {  
  
    NSFileManager *fm = [[NSFileManager alloc] init];  
    NSString *imgKey = [SDWebImageManager.sharedManager cacheKeyForURL:url];  
    NSString *imgPath = [SDWebImageManager.sharedManager.imageCache defaultCachePathForKey:imgKey];  
    NSDictionary *fileAttr = [fm attributesOfItemAtPath:imgPath error:nil];  
  
    NSMutableDictionary *mutableHeaders = [headers mutableCopy];  
  
    NSDate *lastModifiedDate = nil;  
  
    if (fileAttr.count > 0) {  
        if (fileAttr.count > 0) {  
            lastModifiedDate = (NSDate *)fileAttr[NSFileModificationDate];  
        }  
  
    }  
    NSDateFormatter *formatter = [[NSDateFormatter alloc] init];  
    formatter.timeZone = [NSTimeZone timeZoneWithAbbreviation:@"GMT"];  
    formatter.locale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US"];  
    formatter.dateFormat = @"EEE, dd MMM yyyy HH:mm:ss z";  
  
    NSString *lastModifiedStr = [formatter stringFromDate:lastModifiedDate];  
    lastModifiedStr = lastModifiedStr.length > 0 ? lastModifiedStr : @"";  
    [mutableHeaders setValue:lastModifiedStr forKey:@"If-Modified-Since"];  
  
    return mutableHeaders;  
};
```

这段代码是以本地url对应的缓存图片更新时间转换成If-Modified-Since的value值传给服务器，服务器拿到该值后与服务端的Last-Modified值做对比，而本地转换的总是和服务端不相等，导致同一张图片实际上是不断从服务器下载更新了，验证方法：
（1）在沙盒中查看同一张图片的更新时间，是一直变化的
（2）在block中打断点，查看SDImageCacheType 值

加载图片的地方别忘了Option是SDWebImageRefreshCached
```
NSURL *imgURL = [NSURL URLWithString:@"http://handy-img-storage.b0.upaiyun.com/3.jpg"];  
[[self imageView] sd_setImageWithURL:imgURL  
                    placeholderImage:nil  
                             options:SDWebImageRefreshCached]; 
```

更好的做法还是严格的将Last-Modified 或ETag值缓存起来，url为key,下载图片时将Last-Modified或ETag回传给服务器，两个一起传更好。通过查看SDWebImage源码发现，每次下载图片收到服务器响应时，会发送SDWebImageDownloadReceiveResponseNotification通知，将SDWebImageDownloaderOperation 作为参数传出，而SDWebImageDownloaderOperation有response和request，那么就好办了，我们取出response中的Last-Modified和Etag作为value值，同时取出request的RUR.absoluteString作为key,缓存起来，请求图片时将url对应的Last-Modified、ETag取出在SDWebImageDownloader的headersFilter设置一下传给服务器，有服务器来进行对比。有关服务器的对比逻辑请查看以上参考链接。具体代码如下：

```
#pragma mark -  配置sdwebImage headersFilter
-(void)sdWebImageConfig{

    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(sdWebImageDownloadReceiveResponseNotification:) name:SDWebImageDownloadReceiveResponseNotification object:nil];

    SDWebImageDownloader *imgDownloader = SDWebImageManager.sharedManager.imageDownloader;
    imgDownloader.headersFilter  = ^NSDictionary *(NSURL *url, NSDictionary *headers) {

        NSMutableDictionary *mutableHeaders = [headers mutableCopy];

        NSString * lastModifiedStr = [[SBAppCoreInfo getCacheDB] getStrValue:EMGlobal_LastModified_Cache  dataKey:url.absoluteString];
        NSString * etagStr = [[SBAppCoreInfo getCacheDB] getStrValue:EMGlobal_ETag_Cache   dataKey:url.absoluteString];

        [mutableHeaders setValue:lastModifiedStr forKey:@"If-Modified-Since"];
        [mutableHeaders setValue:etagStr forKey:@"If-None-Match"];

        return mutableHeaders;
    };
}

-(void)sdWebImageDownloadReceiveResponseNotification:(NSNotification *)note{
    SDWebImageDownloaderOperation *  downloaderOperation = (SDWebImageDownloaderOperation *)note.object;
    NSString * urlKey = downloaderOperation.request.URL.absoluteString;
     NSHTTPURLResponse *httpResponse = (NSHTTPURLResponse *)downloaderOperation.response;
    //缓存Last-Modified、ETag值
    [[SBAppCoreInfo getCacheDB] setStrValue:EMGlobal_LastModified_Cache dataKey:urlKey dataValue:httpResponse.allHeaderFields[@"Last-Modified"]];
    [[SBAppCoreInfo getCacheDB] setStrValue:EMGlobal_ETag_Cache dataKey:urlKey dataValue:httpResponse.allHeaderFields[@"ETag"]];
}
```

其中 EMGlobal_LastModified_Cache、EMGlobal_ETag_Cache是定义的宏

```
[[SBAppCoreInfo getCacheDB] setStrValue:EMGlobal_LastModified_Cache dataKey:urlKey dataValue:httpResponse.allHeaderFields[@"Last-Modified"]];
[[SBAppCoreInfo getCacheDB] getStrValue:EMGlobal_LastModified_Cache  dataKey:url.absoluteString];
```