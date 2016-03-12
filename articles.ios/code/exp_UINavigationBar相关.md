## UINavigationBar

### 修改导航栏的高度

[如何自定义NavigationBar的高度](http://www.tuicool.com/articles/36vIri)

在需要进行调整的那个ViewController类的ViewWillApplear这个方法对self.navigationController.navigationBar的frame进行赋值，其中frame.size.height变为我们需要的新高度（本例中为84），然后在ViewWillDisappear这个方法中将frame再改回去就可以了

```
- ( void ) viewWillAppear : ( BOOL ) animated {
     [ super viewWillAppear :animated ] ;

     CGRect rect = self . navigationController . navigationBar . frame ;
     self . navigationController . navigationBar . frame = CGRectMake ( rect . origin . x , rect . origin . y , rect . size . width , 84 ) ;
}
- ( void ) viewWillDisappear : ( BOOL ) animated {
     [ super viewWillDisappear :animated ] ;

     CGRect rect = self . navigationController . navigationBar . frame ;
     self . navigationController . navigationBar . frame = CGRectMake ( rect . origin . x , rect . origin . y , rect . size . width , 44 ) ;
}
```

但是，如上设置，原来的left button、title view也会向下移动，所以如下处理：

```
  // ViewWillAppear
  [ self . navigationController . navigationBar setTitleVerticalPositionAdjustment : - 40.0 forBarMetrics : UIBarMetricsDefault ] ;

  // ViewWillDisappear
  [ self . navigationController . navigationBar setTitleVerticalPositionAdjustment : 0.0 forBarMetrics : UIBarMetricsDefault ] ;
```

  >> UINavigationBar下：
  - (void)setTitleVerticalPositionAdjustment:(CGFloat)adjustment forBarMetrics:(UIBarMetrics)barMetrics
官方描述： Sets the title’s vertical position adjustment for given bar metrics. 调整某个BarMetrics（这个不知道怎么翻译）下标题的垂直位置。
UIBarButtonItem下：
  - (void)setBackgroundVerticalPositionAdjustment:(CGFloat)adjustment forBarMetrics:(UIBarMetrics)barMetrics
官方描述： Sets the background vertical position offset for given bar metrics.This offset is used to adjust the vertical centering of bordered bar buttons within the bar. 调整某个BarMetrics（这个不知道怎么翻译）下背景的垂直位置的偏差值，这个偏差值用来调整该圆角按钮的垂直中心在Bar内的位置。

所以最终看起来是这样
```
#define kExtendNavigationBarHeight  84
#undef  kNavigationBarHeight
#define kNavigationBarHeight        44

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];

    // 导航栏高度调整
    CGRect rect = self.navigationController.navigationBar.frame;
    self.navigationController.navigationBar.frame = CGRectMake(rect.origin.x, rect.origin.y, rect. size.width, kExtendNavigationBarHeight);
    [self.navigationController.navigationBar setTitleVerticalPositionAdjustment:kNavigationBarHeight-kExtendNavigationBarHeight
                                                                 forBarMetrics: UIBarMetricsDefault];
    [self.navigationItem.leftBarButtonItem setBackgroundVerticalPositionAdjustment:kNavigationBarHeight-kExtendNavigationBarHeight
                                                                     forBarMetrics:UIBarMetricsDefault];
}

- (void)viewWillDisappear:(BOOL)animated{
    [super viewWillDisappear:animated];

    // 开启iOS7返回手势
    if ([self.navigationController respondsToSelector:@selector(interactivePopGestureRecognizer)]) {
        self.navigationController.interactivePopGestureRecognizer.enabled = YES;
    }

    CGRect rect = self.navigationController.navigationBar.frame;
    self.navigationController.navigationBar.frame = CGRectMake(rect.origin.x, rect.origin.y, rect. size.width, kNavigationBarHeight);
    [self.navigationController.navigationBar setTitleVerticalPositionAdjustment:0.f
                                                                 forBarMetrics:UIBarMetricsDefault];

    [self.navigationItem.leftBarButtonItem setBackgroundVerticalPositionAdjustment:0.f
                                                                     forBarMetrics:UIBarMetricsDefault];
}

```

  * 终极方案：不用系统的NavigationBar啦，咱们自定义一个
