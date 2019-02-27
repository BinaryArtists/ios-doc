## App 启动流程

	* [app 启动原理](https://github.com/BinaryArtists/not-just-code/blob/master/ios/architect/app.start-procedure.md)

### iOS App 生命周期状态

苹果对UIApplicationState的定义如下：
```
typedef enum : NSInteger {
   UIApplicationStateActive,
   UIApplicationStateInactive,
   UIApplicationStateBackground 
} UIApplicationState;
```

我对它的重新定义，分三个Stage：
```
typedef enum : NSUInteger {
    AppStage_Terminated, // 关闭阶段
    AppState_Background, // 后台阶段
    。。还有一个准备态
    AppState_Running,    // 运行阶段
} AppStateType;
```

同时也定义了一套app系列类对象，主要是便于管理，也可以实现一个应用中，实际存在多个app抽象体：
```
ApplicationManager,
Application,    区别于苹果应用的app抽象，类似一个外观类
AppContext,		app上下文，状态控制
AppAppearance,	app外观
AppConfig,		app配置信息
AppModule,		app基础模块管理
AppInitializer,	app初始化对象
AppUinitializer,app反初始化对象
```

以上，其实是典型的自上而下设计，容易出现过设计，但暂时没发现什么不妥。

再者，对AppContext上下文，定了几个状态
```
typedef enum : NSUInteger {
    AppContext_Ownerless, // 无主态，未登录，该状态下用户仍然可以玩耍，但在接口请求的时候，进行截获，需要的时候，给予登录提示
    AppContext_Logined,   // 登录态
} AppContextType;
```

### iOS App 启动流程：

先看一张图

![start routine image](https://github.com/BinaryArtists/not-just-code/blob/master/ios/imges/start-routine.png)

对上面这张图，做一下解释：

1. 启动页面，可以长时间停驻，可以用启动图＋启动Viewcontroller（保持两者一模一样），来实现。
2. 外部依赖模块和内部基础模块，不相互影响
3. 基础模块是被现场恢复依赖的，需要串行
4. 从启动页面到主界面，一般而言，中间的两部一般都要做完


