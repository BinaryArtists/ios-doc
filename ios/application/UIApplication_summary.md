## UIApplication的详细介绍

参考：[UIApplication的详细介绍](http://www.jianshu.com/p/a72bcb948371)

### UIApplication

* 什么是UIApplication?

UIApplication对象是应⽤程序的象征.
每一个应用都有⾃己的UIApplication对象,这个对象是系统自动帮我们创建的, 它是一个单例对象.
一个iOS程序启动后创建的第一个对象就是UIApplication对象
我们只能通过[UIApplication sharedApplication]获得这个单例对象,不能够手动去创建它.

* UIApplication作用?

利⽤用UIApplication对象,能进⾏一些应用级别的操作.
可以设置应用程序图标右上角的红色提醒数字
设置联⽹指示器的可见性
可以设置应用程序的状态栏，进行应用之间的跳转.

### 模仿UIApplication单例

* 要求:程序一启动就创建创建对象.
创建的对象只能通过share的方式获取对象.
不能够进行alloc 操作,当执行alloc时, 程序发生崩溃

1.程序一启动的时候就创建对象.
当类被加载到内存的时候就会调用load的方法,它会比main方法优先调用
```
static Persion *_instance;
+ (void)load {
    想要保存创建的对象, 要用成员属性,但是现在是类方法, 没有办法访问成员属性. 所以搞了一个static的静态变量进行保存
    创建对象
    _instance = [[self alloc] init];
}
```

2.保证调用alloc时会产⽣异常, 重写系统的是alloc方法
```
 +(instancetype)alloc {
  当调用alloc方法时,先查看一下_instance有没有值,如果已经有值的话,直接抛出异常.
  if(_instance) {
      NSException *exception = [NSException exceptionWithName: @"NSInternalInconsistencyException"
                                                       reason:
      @"There can only be one Persion instance." userInfo:nil]; 抛出异常
      [exception raise];
  }

  保持系统的做法.
  return [super alloc];
}
```

3.通过share方式获取对象
```
+ (instancetype)sharePersion {
  return _instance
}
```

### UIApplication功能

* 设置应用提醒数字
获取UIApplication对象
```
UIApplication *ap = [UIApplication sharedApplication];
在设置之前, 要注册一个通知,从ios8之后,都要先注册一个通知对象.才能够接收到提醒. UIUserNotificationSettings *notice =
[UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeBadge categories:nil];
注册通知对象
[ap registerUserNotificationSettings:notice];
设置提醒数字
ap.applicationIconBadgeNumber = 20;
```

* 设置连网状态
```
ap.networkActivityIndicatorVisible = YES;
```

* 设置状态栏

默认情况
应用程序的状态栏,默认是交给控制器来管理的.
控制器提供的方法,可以直接重写这个方法
在控制器当中设置状态栏样式
```
- (UIStatusBarStyle)preferredStatusBarStyle {
    return UIStatusBarStyleLightContent;
    }
```

设置完成后
隐藏状态栏,通过控制器的方式.同样实现方法: 返回NO时为不隐藏, 返回YES时为隐藏
```
- (BOOL)prefersStatusBarHidden {
    return NO;
    }
```

通常在开发当中都是应用程序来管理状态栏的.来做统一管理,不然的话, 会有很多个控制器. 会非常的麻烦.
想要让应用程序管理状态栏,要在info.plist当中进行配置,
在最后一个添加一个key值:View controller-based status bar appearance
设置为NO.就是应用程序来管理了. 并且控制器管理会无效
通过UIApplication来管理状态.

1.获取UIApplication
UIApplication * ap = [UIApplication sharedApplication];
2.设置状态栏样式.
ap.statusBarStyle = UIStatusBarStyleLightContent;
3.设置状态的隐藏
ap.statusBarHidden = YES;
4.跳转网页
UIApplication * ap = [UIApplication sharedApplication]; URL:协议头://域名 应用程序通过协议头的类型,去打开相应的软件.
NSURL * url =[NSURL URLWithString:@"http://www.baidu.com"]; [ap openURL:url];
打电话
[application openURL:[NSURL URLWithString:@"tel://10086"]];
发短信
[app openURL:[NSURL URLWithString:@"sms://10086"]];

### UIApplication代理

所有的移动操作系统都有个致命的缺点:app很容易受到打扰。
比如一个来电或者锁屏会导致app进入后台甚至被终止,还有很多其它类似的情况会导致app受到干扰,在app受到干扰时,会产生一些系统事件, 这时UIApplication会通知它的delegate对象,让delegate代理来处理这些系统事件
delegate可处理的事件包括:

应用程序的生命周期事件(如程序启动和关闭)
系统事件(如来电)
内存警告
...
UIApplication会在程序一启动时候创建一个遵守UIApplicationDelegate的代理.
这个就是我们程序一创建时的AppDelegate类。AppDelegate就是遵守了UIApplicationDelegate协议。 在这个类中定义很多监听系统事件的方法.同时也定义了一些应用程序的生命周期方法.

主要⽅法有:
应用程序的生命周期
应用程序启动完成的时候调用
- (BOOL)application:(UIApplication * )application didFinishLaunchingWithOptions: (NSDictionary * )launchOptions {
    return YES;
}
当我们应用程序即将失去焦点的时候调用
- (void)applicationWillResignActive:(UIApplication * )application {
NSLog(@"%s",__func__);
}
当我们应用程序完全进⼊后台的时候调用
- (void)applicationDidEnterBackground:(UIApplication * )application{
NSLog(@"%s",__func__);
}
当我们应用程序即将进⼊前台的时候调用
- (void)applicationWillEnterForeground:(UIApplication * )application {
NSLog(@"%s",__func__);}
当我们应用程序完全获取焦点的时候调用
只有当一个应用程序完全获取到焦点,才能与用户交互.
- (void)applicationDidBecomeActive:(UIApplication * )application {
- NSLog(@"%s",__func__);
}
当我们应用程序即将关闭的时候调用
- (void)applicationWillTerminate:(UIApplication * )application {
NSLog(@"%s",__func__);
}
应⽤程序的启动原理(重要)
程序启动时执行main函数,在main函数当中有以下操作.

int main(int argc, char * argv[]) {
  @autoreleasepool {
      return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
       第三个参数:UIApplication类名或者子类的名称 nil == @"UIApplication"
NSStringFromClass好处:1.有提示功能 2.避免输入错误
  }
}
程序完整启动流程

1.执行Main
2.执行UIApplicationMain函数.
3.创建UIApplication对象,并设置UIApplicationMain对象的代理.
UIApplication的第三个参数就是UIApplication的名称,如果指定为nil,它会默认 为UIApplication.
UIApplication的第四个参数为UIApplication的代理.
4.开启一个主运行循环.保证应用程序不退出.
5.加载info.plist.加载配置⽂文件.判断一下info.plist文件当中有没有Main storyboard file base name
⾥面有没有指定storyboard文件,如果有就去加载info.plist文件,如果没有,那么应用程序加载完毕.
6.通知应用程序，调用代理方法
