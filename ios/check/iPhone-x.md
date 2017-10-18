# iPhone X 适配

## 状态栏

iPhone X状态条由20px变成了44px，UITabBar由49px变成了83px。设置布局时y直接写成64的就要根据机型设置。可以设置宏
```
#define Device_Is_iPhoneX ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(1125, 2436), [[UIScreen mainScreen] currentMode].size) : NO)，
```

## 控制器生命周期之viewSafeAreaInsetsDidChange

viewDidLoad
viewWillAppear
viewSafeAreaInsetsDidChange（NEW）
viewWillLayoutSubviews
viewDidAppear

可以在 viewSafeAreaInsetsDidChange 后，设置 Safe Area
```
if #available(iOS 11.0, *) {
   self.additionalSafeAreaInsets = UIEdgeInsetsMake(10, 0, 0, 34)
}
```