## 启动时间优化

启动时间：从程序响应到绘取第一帧的时间
1. http://blog.csdn.net/u011619283/article/details/53135850
2. http://www.cocoachina.com/ios/20161102/17931.html
3. http://blog.csdn.net/wadahana/article/details/50315089

http://ios.jobbole.com/90331/

http://www.cocoachina.com/ios/20160104/14870.html

### 启动时间 定义

参考：[今日头条iOS客户端启动速度优化](http://blog.csdn.net/u011452278/article/details/54966682)


t(App总启动时间) = t1(main()之前的加载时间) + t2(main()之后的加载时间)。 t1 = 系统dylib(动态链接库)和自身App可执行文件的加载；
t2 = main方法执行之后到AppDelegate类中的
```
- (BOOL)Application:(UIApplication *)Application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```
方法执行结束前这段时间

### 启动时间 测算

在main.c中
```
CFAbsoluteTime StartTime;
int main(int argc, char **argv) {
StartTime = CFAbsoluteTimeGetCurrent();
```

在AppDelegate中
```
dispatch_async(dispatch_get_main_queue(), ^{
NSLog(@”Lauched in %f seconds.”, (CFAbsoluteTimeGetCurrent() – StartTime));
});
```

### 链接和载入

可以在Time Profile中显示dyld载入库函数，库会被映射到地址空间，同时完成绑定以及静态初始化

执行从dyld开始，dyld从可执行文件的依赖开始, 递归加载所有的依赖动态链接库。
动态链接库包括：iOS 中用到的所有系统 framework，加载OC runtime方法的libobjc，系统级别的libSystem，例如libdispatch(GCD)和libsystem_blocks (Block)

### UIKit初始化

如果应用的Root View Controller是由XIB实现的，也会在启动时被初始化。


### 应用回调

调用UIApplicationDeleagte的回调：application:didFinishLaunchingWithOptions


### 第一次Core Animation调用

在启动后的方法-[UIApplication _resportAppLaunchFinished_ 中调用CA::Transaction::commit实现第一帧画面的绘制
