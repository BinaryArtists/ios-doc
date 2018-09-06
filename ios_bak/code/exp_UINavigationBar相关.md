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

但是，要注意啊，获取self.navigationItem.leftBarButtonItem的时候，要保证这个item，一定是backButton，不然没有效果的。

下面是我的做法：
1. 自定义leftButtonItems
2. 需要重设高度的时候，则从leftButtonItems中取出必要的控件，看下面骚气的代码：
```
UIBarButtonItem *backButtonItem = [self.navigationItem.leftBarButtonItems lastObject];
    UIView *leftButtonHolderView = (UIView *)backButtonItem.customView;
    UIButton *backButon = (UIButton *)leftButtonHolderView;

    [backButon setContentEdgeInsets:UIEdgeInsetsMake(verticalOffset, 0, -verticalOffset, 0)];
```

哈哈哈，解决问题。当然，如果不改变系统控件定制方式，由要是点击区域严格，则参考下面的做法吧：
[UIBarButtonItem:setBackgroundVerticalPositionAdjustment not working properly with custom button]((http://stackoverflow.com/questions/16444941/uibarbuttonitemsetbackgroundverticalpositionadjustment-not-working-properly-wit))

关键代码：
```
UIButton *locationButtonAux = [UIButton buttonWithType:UIButtonTypeCustom];
    [locationButtonAux setFrame:CGRectMake(0, 0, LOCATION_BUTTON_WIDTH, LOCATION_BUTTON_HEIGHT)];
    [locationButtonAux setBackgroundImage:[UIImage imageNamed:@"location_button.png"] forState:UIControlStateNormal];
    [locationButtonAux setBackgroundImage:[UIImage imageNamed:@"location_button.png"] forState:UIControlStateHighlighted];
    [locationButtonAux addTarget:self action:@selector(userLocation) forControlEvents:UIControlEventTouchUpInside];

    UIView *rightView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 150, 30)]; //change this frame to counteract for your taller navbar
    [rightView addSubview:locationButtonAux];
    UIBarButtonItem *rightBarButtonItem = [[UIBarButtonItem alloc] initWithCustomView:rightView];
    self.navigationItem.rightBarButtonItem = rightBarButtonItem;
```

  * 终极方案：不用系统的NavigationBar啦，咱们自定义一个导航栏
  可以参考：[第三方库](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/library.thirdpart.md)中的“导航派”，啦啦啦～


### [对leftItemsSupplementBackButton属性的认识](http://mmz06.blog.163.com/blog/static/12141696201110288374588/)
API对leftItemsSupplementBackButton的解释

A Boolean value indicating whether the left items are displayed in addition to the back button.

Discussion

Normally, the presence of custom left bar button items causes the back button to be removed in favor of the custom items. Setting this property to YES causes the items in the leftBarButtonItems or leftBarButtonItem property to be displayed to the right of the back button—that is, they are displayed in addition to, and not instead of, the back button. When set to NO, the items in those properties are displayed instead of the back button. The default value of this property is NO.

说明：此值是一个布尔值，决定是否显示back button.

使用

当某个UIViewController自定义了LeftBarButtonItem(如：返回首页的Home按钮)，同时这个UIViewController存在被其他UIViewController，pushViewController(即：从其他试图控制器，跳转到自定义LeftBarButtonItem的试图控制器),这时如果没有在自定义LeftBarButtonItem设置UINavigationItem的属性leftItemsSupplementBackButton，那么系统会默认为NO，那麽其他控制器跳转到此控制器时，导航栏就只会展示你自定的LeftBarButtonItem，而不会显示后退按钮。但这样很不友好。如果想显示后退按钮，这在自定义LeftBarButtonItem时，设置UINavigationItem的属性leftItemsSupplementBackButton为YES即可。

其他

iMilo

2011/11/28

### [IOS UINavigationController 自定义返回按钮](http://blog.sina.com.cn/s/blog_7124765801013oo9.html)

[UINavigationController Class Reference]
The bar button item on the left side of the navigation bar allows for navigation back to the previous view controller on the navigation stack. The navigation controller updates the left side of the navigation bar as follows:

If the new top-level view controller has a custom left bar button item, that item is displayed. To specify a custom left bar button item, set the leftBarButtonItem property of the view controller’s navigation item.
If the top-level view controller does not have a custom left bar button item, but the navigation item of the previous view controller has a valid item in itsbackBarButtonItem property, the navigation bar displays that item.
If a custom bar button item is not specified by either of the view controllers, a default back button is used and its title is set to the value of the title property of the previous view controller—that is, the view controller one level down on the stack. (If there is only one view controller on the navigation stack, no back button is displayed.)

使用pushViewController切换到下一个视图时，navigation controller按照以下3条顺序更改导航栏的左侧按钮。

1、如果B视图有一个自定义的左侧按钮（leftBarButtonItem），则会显示这个自定义按钮；
2、如果B没有自定义按钮，但是A视图的backBarButtonItem属性有自定义项，则显示这个自定义项；
3、如果前2条都没有，则默认显示一个后退按钮，后退按钮的标题是A视图的标题。

按照这个解释，我把UIBarButtonItem *backItem ……这段代码放在A视图的pushViewController语句之前。

OK问题解决了，B视图的后退按钮的标题变成back了。

1 2 3 4
UIBarButtonItem *backItem = [[UIBarButtonItem alloc] initWithTitle:@"back" style:UIBarButtonItemStyleBordered target:nil action:nil]; [self.navigationItem setBackBarButtonItem:backItem]; [backItem release]; [self.navigationController pushViewController:self.bView animated:YES];

**

###  UINavigationControllerDelegate


### UINavigationBar设置shadowImage

/* Default is nil. When non-nil, a custom shadow image to show instead of the default shadow image. For a custom shadow to be shown, a custom background image must also be set with -setBackgroundImage:forBarMetrics: (if the default background image is used, the default shadow image will be used).

想要设置shadowImage必须要先设置navigationBar的backgroundImage。
