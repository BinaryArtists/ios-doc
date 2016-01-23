links:http://blog.sina.com.cn/s/blog_bf9843bf0101l53o.html

旧工程配置arc方案：
1,直接在targets->build phases中修改compiler Flags,是否支持arc。添加：-fobjc-arc，就可以让旧项目支持arc。如果想让原来支持arc的不使用arc则添加-fno-objc-arc
如图：工程里ARC的设置和注意事项
2,因为在build phases中可以改变是否支持arc，所以应该在代码中添加判断是否支持arc，这样不管以后.m的arc是否改变，都不用再次调整代码。
下面是一个.h文件(一下附件有.h文件)，整合了arc的各种属性、release判断，直接#import在你想使用arc的类中即可。
#ifndef paixiu_PXISARC_h
#define paixiu_PXISARC_h
#ifndef PX_STRONG
#if __has_feature(objc_arc)
#define PX_STRONG strong
#else
#define PX_STRONG retain
#endif
#endif
#ifndef PX_WEAK
#if __has_feature(objc_arc_weak)
#define PX_WEAK weak
#elif __has_feature(objc_arc)
#define PX_WEAK unsafe_unretained
#else
#define PX_WEAK assign
#endif
#endif

#if __has_feature(objc_arc)
#define PX_AUTORELEASE(expression) expression
#define PX_RELEASE(expression) expression
#define PX_RETAIN(expression) expression
#else
#define PX_AUTORELEASE(expression) [expression autorelease]
#define PX_RELEASE(expression) [expression release]
#define PX_RETAIN(expression) [expression retain]
#endif
#endif
说明：在arc中，strong对应原来的retain与copy，weak对应原来的assign。
EX：举例使用autorelease：
NSArray *testArray = PX_AUTORELEASE([[NSArray alloc] init]);
 //如果支持arc，testArray就只是alloc init，release的事情由系统来做。
//如果不支持arc，那这条语句相当于：
NSArray *testArray = [[[NSArray alloc] init] autorelease];
这样不管以后改不改arc，都不会内存泄漏了 .
所以，arc的使用有两点：
A:在build phases中修改compiler Flags值。
B:在代码中判断是否支持arc，包括对属性（property）、释放（release）的判断。
3,在dealloc中需要这样做：
类如果注册了通知（观察者模式），需要remove掉。这个不管是否支持arc，都必须要做的。
- (void)dealloc {
[[NSNotificationCenterdefaultCenter] removeObserver:self];//如果注册了通知的话。
[self removeObserver:self forKeyPath:keyPath];//如果注册了kvo的话。
#if !__has_feature(objc_arc)  //在这里也需要判断是否支持arc，支持的话就执行旧工程中该release的语句.
    [array release]; //array代表alloc但没有autorelease的变量
    [super dealloc];
#endif
}
4，另外加点block的判断，这个是在4.0以后有的，当然也可以不进行判断，因为现在大多数都4.0以后了。
#if NS_BLOCKS_AVAILABLE
#endif
 
总结：
1,arc的设置是在build phases中修改compiler Flags的值。
2,如果使用了arc，在你的代码中不可以使用retain, release, autorelease，如果使用的话会报错。
3,如果使用了arc，在@property声明中，用strong和weak代替相应的retain, copy,和assign。
4,如果使用了arc，NSAutoReleasePool也不能使用，测试发现，用@autoreleasepool 代替，不会编译报错。
 
总之，一切你之前“背过”的那几条内存管理规则，你都不用去管了。而且，个人感觉，用arc代码清晰很多，而且效率也提高了些。但是如果使用不当的话也易造成内存泄露问题，还是根据自己情况酌情使用。
