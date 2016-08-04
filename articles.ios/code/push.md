## 推送

1. [apns 总结心得，从小白到大白！后台，退出等情况](http://blog.csdn.net/ss_aa_aa/article/details/52066504)

###

根据上面可以得出：要想实现消息推送，必须满足1个前提5个步骤。
前提：必须真机才可以实现。
步骤：
1.你的IOS应用需要去注册APNS消息推送功能。
2.当苹果APNS推送服务器收到来自你的应用的注册消息就会返回一串device token给你（很重要）
3.将应用收到的device Token传给你本地的Push服务器。
4.当你需要为应用推送消息的时候，你本地的推送服务器会将消息，以及Device Token打包发送到苹果的APNS服
5.APNS再将消息推送给目的iphone对应的应用

## 后台，退出等情况下收到推送

1. 远程－－后台启动－－走的代理
```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
```

2. 远程－－后台启动－－走的代理同上  判断状态 (当你有挂起的通知时候，你直接点击应用程序的图标Application icon来启动应用程序时，系统是不会调用didReceiveLocalNotification方法，所以你是接收不到推送通知的消息的)
```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
{
    // 让SDK得到App目前的各种状态，以便让SDK做出对应当前场景的操作
    if (application.applicationState == UIApplicationStateActive)//正在操作状态
    {
     }
    if(application.applicationState == UIApplicationStateBackground)//暂没研究到
    {
    }
    if(application.applicationState == UIApplicationStateInactive)//后台运行
    {
如果你需要跳转一个界面，，比如一个url网页可以这样来  效果不错
                [[UIApplication sharedApplication]openURL:[NSURL URLWithString:myUrl]];
    }
}
```

3. 远程－－后台退出情况下，程序全部关闭－－走的代理
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions

这个接口很厉害啊 程序每次启动的时候都是走的这个，，，，，，当我们点击icon进入的时候  是不会有参数的 ，但是当我们是收到远程通知点击事件进入的时候  会携带参数，launchOptions，这个时候重点来了，，，我们需要通过以下方法获得
    if (launchOptions) {
        //获取应用程序消息通知标记数（即小红圈中的数字）
        NSInteger badge = [UIApplication sharedApplication].applicationIconBadgeNumber;
        if (badge>0)
        {
            //如果应用程序消息通知标记数（即小红圈中的数字）大于0，清除标记。
            badge--;
            //清除标记。清除小红圈中数字，小红圈中数字为0，小红圈才会消除。
            [UIApplication sharedApplication].applicationIconBadgeNumber = badge;

        }
        NSDictionary *userInfo = [launchOptions objectForKey:@"UIApplicationLaunchOptionsRemoteNotificationKey"];

            //获取推送详情

如果你需要跳转一个界面，，比如一个url网页可以这样来  效果不错
                [[UIApplication sharedApplication]openURL:[NSURL URLWithString:myUrl]];

    }
```
