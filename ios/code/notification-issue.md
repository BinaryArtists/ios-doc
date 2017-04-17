# notification

参考：[iOS 下APNS推送处理函数详解](http://www.2cto.com/kf/201410/344169.html)

## 工程配置说明

还没落实

## 过程函数说明

## 运行过程说明

## 版本迭代坑点

### iOS 10 之前
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```
   
这是在程序结束启动，并即将运行时调用的，通常初始化的工作可以在这个函数中处理。同样的，推送的相关初始化操作也需要在这个部分完成。这一部分的工作主要分为两部分： 

1. 推送类型的注册：

```
[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeBadge |UIRemoteNotificationTypeSound | UIRemoteNotificationAlert];
```

这行代码告诉了系统，该程序注册的推送消息类型，通常包括badge、声音以及alert通知。 

2. 处理程序没有启动时的推送消息： 如果是程序正在运行或者说程序正在后台，那么这个时候处理推送消息的工作都是在：
```
-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo 或者：

-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
```
中完成。但是如果用户点击推送通知的时候程序还没有被启动，这个时候以上两个函数都是接收不到用户的推送通知的，这个时候需要在application：（UIApplication *）application didFinishLaunchingWithOptions：（NSDictionary*) launchOptions 函数里面进行处理。而推送消息的相关信息就存储在launchOptions这个字典里。具体参如下代码：
```
NSDictionary* pushInfo = [launchOptions objectForKey:@"UIApplicationLaunchOptionsRemoteNotificationKey"];
if (pushInfo)
{
    NSDictionary *apsInfo = [pushInfo objectForKey:@"aps"];
    if(apsInfo)
    {
        //your code here
    }
     
}
```

```
- (void)application:(UIApplication *)applicationdidRegisterForRemoteNotificationsWithDeviceToken:(NSData *)pToken & - (void)application:(UIApplication *)applicationdidFailToRegisterForRemoteNotificationsWithError:(NSError *)error
```
为了让device端可以接收到推送消息，需要将设备的token传送到苹果的服务器，这个token就相当于设备的识别码，每一台苹果设备都有唯一的token，苹果的服务器就是通过这个token找到对应的设备，并传送相应地消息。这两个函数就是在传送token成功或者失败后调用的，用户在对应的函数里面做一些相应地处理。 

```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo和

-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
```

都是程序在运行过程中（无论当前程序处于前台还是后台）接收到推送消息的处理函数。根据苹果的官方文档，建议大家使用

```
-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
```

因为前者在程序处于后台的时候是无法接收到推送信息的（经实测-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo其实可以接收到，不知道是怎么回事，希望大虾解疑）。另外就是-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler 还有一个作用。根据苹果给出的文档，系统给出30s的时间对推送的消息进行处理，此后就会运行CompletionHandler程序块。

在处理这类推送消息（即程序被启动后接收到推送消息）的时候，通常会遇到这样的问题，就是当前的推送消息是当前程序正在前台运行时接收到的还是说是程序在后台运行，用户点击系统消息通知栏对应项进入程序时而接收到的？这个其实很简单，用下面的代码就可以解决：

void application:(UIApplication*)application didReceiveRemoteNotification:NSDictionary）userInfo fetchCompletionHandler:((^)UIBackgroundFetchResult)completionHandler{
if (application.applicationState == UIApplicationStateActive) {
        NSLog(@"active");
        //程序当前正处于前台
    }
    else if(application.applicationState == UIApplicationStateInactive)
    {
        NSLog(@"inactive");
        //程序处于后台
         
    }
}


关于userInfo的结构，参照苹果的官方结构：
?
1
{
?
1
"aps" : {
?
1
"alert" : "You got your emails.",
?
1
"badge" : 9,
?
1
"sound" : "bingbong.aiff"
?
1
 
?
1
"acme1" : "bar",
?
1
"acme2" : 42
?
1
}
?
1
 
即key aps对应了有一个字典，里面是该次推送消息的具体信息。具体跟我们注册的推送类型有关。另外剩下的一些key就是用户自定义的了。

### iOS 10 之后