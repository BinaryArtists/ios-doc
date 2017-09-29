# iPhone X 适配

## 状态栏

iPhone X状态条由20px变成了44px，UITabBar由49px变成了83px。设置布局时y直接写成64的就要根据机型设置。可以设置宏
```
#define Device_Is_iPhoneX ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(1125, 2436), [[UIScreen mainScreen] currentMode].size) : NO)，
```