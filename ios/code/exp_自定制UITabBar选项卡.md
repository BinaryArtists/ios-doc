## 从几个开源代码看UITabBar选项卡的自定制


### [LBCoderLee/iOS_XianYu](https://github.com/LBCoderLee/iOS_XianYu)

主要思路和核心代码：

	* 1. 用自定制TabBar来替换系统TabBar
	```
	- (void)viewDidLoad {
	    [super viewDidLoad];

	    [self setUpAllChildVc];

	    //创建自己的tabbar，然后用kvc将自己的tabbar和系统的tabBar替换下
	    LBTabBar *tabbar = [[LBTabBar alloc] init];
	    tabbar.myDelegate = self;
	    //kvc实质是修改了系统的_tabBar
	    [self setValue:tabbar forKeyPath:@"tabBar"];
	}
	```

	* 2. 设置单个tabBarButton
	```
	- (void)setUpOneChildVcWithVc:(UIViewController *)Vc Image:(NSString *)image selectedImage:(NSString *)selectedImage title:(NSString *)title
	{
	    LBNavigationController *nav = [[LBNavigationController alloc] initWithRootViewController:Vc];


	    Vc.view.backgroundColor = [self randomColor];

	    UIImage *myImage = [UIImage imageNamed:image];
	    myImage = [myImage imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];

	    //tabBarItem，是系统提供模型，专门负责tabbar上按钮的文字以及图片展示
	    Vc.tabBarItem.image = myImage;

	    UIImage *mySelectedImage = [UIImage imageNamed:selectedImage];
	    mySelectedImage = [mySelectedImage imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];

	    Vc.tabBarItem.selectedImage = mySelectedImage;

	    Vc.tabBarItem.title = title;

	    Vc.navigationItem.title = title;

	    [self addChildViewController:nav];
	    
	}
	```


	* 3. 特殊bar样式：两边的控制权仍然是UITabBarController，中间的由自己画，需要调整位置：
	```
	- (void)layoutSubviews
	{
	    [super layoutSubviews];
	    //系统自带的按钮类型是UITabBarButton，找出这些类型的按钮，然后重新排布位置，空出中间的位置
	    Class class = NSClassFromString(@"UITabBarButton");

	    self.plusBtn.centerX = self.centerX;
	    //调整发布按钮的中线点Y值
	    self.plusBtn.centerY = self.height * 0.5 - 2*LBMagin ;

	    self.plusBtn.size = CGSizeMake(self.plusBtn.currentBackgroundImage.size.width, self.plusBtn.currentBackgroundImage.size.height);

	        UILabel *label = [[UILabel alloc] init];
	        label.text = @"发布";
	        label.font = [UIFont systemFontOfSize:11];
	        [label sizeToFit];
	        label.textColor = [UIColor grayColor];
	        [self addSubview:label];
	        label.centerX = self.plusBtn.centerX;
	        label.centerY = CGRectGetMaxY(self.plusBtn.frame) + LBMagin ;

	    int btnIndex = 0;
	    for (UIView *btn in self.subviews) {//遍历tabbar的子控件
	        if ([btn isKindOfClass:class]) {//如果是系统的UITabBarButton，那么就调整子控件位置，空出中间位置
	            //每一个按钮的宽度==tabbar的五分之一
	            btn.width = self.width / 5;

	            btn.x = btn.width * btnIndex;

	            btnIndex++;
	            //如果是索引是2(从0开始的)，直接让索引++，目的就是让消息按钮的位置向右移动，空出来发布按钮的位置
	            if (btnIndex == 2) {
	                btnIndex++;
	            }
	            
	        }
	    }
	    
	    [self bringSubviewToFront:self.plusBtn];
	}
	```

	* 4. 如果特殊的button样式，超出了bar？重写hitTest方法，去监听发布按钮的点击，目的是为了让凸出的部分点击也有反应
	```
	- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
	    //这一个判断是关键，不判断的话push到其他页面，点击发布按钮的位置也是会有反应的，这样就不好了
	    //self.isHidden == NO 说明当前页面是有tabbar的，那么肯定是在导航控制器的根控制器页面
	    //在导航控制器根控制器页面，那么我们就需要判断手指点击的位置是否在发布按钮身上
	    //是的话让发布按钮自己处理点击事件，不是的话让系统去处理点击事件就可以了
	    if (self.isHidden == NO) {

	        //将当前tabbar的触摸点转换坐标系，转换到发布按钮的身上，生成一个新的点
	        CGPoint newP = [self convertPoint:point toView:self.plusBtn];

	        //判断如果这个新的点是在发布按钮身上，那么处理点击事件最合适的view就是发布按钮
	        if ( [self.plusBtn pointInside:newP withEvent:event]) {
	            return self.plusBtn;
	        }else{//如果点不在发布按钮身上，直接让系统处理就可以了

	            return [super hitTest:point withEvent:event];
	        }
	    }

	    else {//tabbar隐藏了，那么说明已经push到其他的页面了，这个时候还是让系统去判断最合适的view处理就好了
	        return [super hitTest:point withEvent:event];
	    }
	}
	```

### [jiangyongjian/IrregularTabBar](https://github.com/jiangyongjian/IrregularTabBar)

主要思路和代码：

	* 1. 加自定制的tabbar，加至系统tabbar
	```
	- (void)setUpTabBar
	{
	    BXTabBar *tabBar = [[BXTabBar alloc] init];
	    // 存储UITabBarItem
	    tabBar.items = self.items;
	    
	    tabBar.delegate = self;
	    
	    tabBar.backgroundColor = [UIColor colorWithPatternImage:[UIImage imageNamed:@"tab_background"]];
	    
	    tabBar.frame = self.tabBar.frame;
	    [self.view addSubview:tabBar];
	    self.mytabbar = tabBar;
	}
	```

	* 2. 添加一个自控制器（ButtonItem设置一样，不一样的是，加了navVC代理）
	```
	- (void)addOneChildVC:(UIViewController *)childVc title:(NSString *)title imageName:(NSString *)imageName selectedImageName:(NSString *)selectedImageName {
	    // 设置标题
	    childVc.tabBarItem.title = title;
	    
	    // 设置图标
	    childVc.tabBarItem.image = [UIImage imageNamed:imageName];
	    
	    // 设置选中的图标
	    UIImage *selectedImage = [UIImage imageNamed:selectedImageName];
	    // 不要渲染
	    selectedImage = [selectedImage imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
	    childVc.tabBarItem.selectedImage = selectedImage;
	    
	    // 记录所有控制器对应按钮的内容
	    [self.items addObject:childVc.tabBarItem];
	    
	    // 添加为tabbar控制器的子控制器
	    BXNavigationController *nav = [[BXNavigationController alloc] initWithRootViewController:childVc];

	    nav.delegate = self;
	    [self addChildViewController:nav];
	}
	```

	```
	#pragma mark navVC代理
	- (void)navigationController:(UINavigationController *)navigationController willShowViewController:(UIViewController *)viewController animated:(BOOL)animated{
	    UIViewController *root = navigationController.viewControllers.firstObject;
	    self.tabBar.hidden = YES;
	    if (viewController != root) {
	        //从HomeViewController移除
	        [self.mytabbar removeFromSuperview];
	        // 调整tabbar的Y值
	        CGRect dockFrame = self.mytabbar.frame;

	        dockFrame.origin.y = root.view.frame.size.height - kTabbarHeight;
	        if ([root.view isKindOfClass:[UIScrollView class]]) { // 根控制器的view是能滚动
	            UIScrollView *scrollview = (UIScrollView *)root.view;
	            dockFrame.origin.y += scrollview.contentOffset.y;
	        }
	        self.mytabbar.frame = dockFrame;
	        // 添加dock到根控制器界面
	        [root.view addSubview:self.mytabbar];
	    }
	}

	// 完全展示完调用
	-(void)navigationController:(UINavigationController *)navigationController didShowViewController:(UIViewController *)viewController animated:(BOOL)animated{
	    UIViewController *root = navigationController.viewControllers.firstObject;
	    BXNavigationController *nav = (BXNavigationController *)navigationController;
	    if (viewController == root) {
	        // 更改导航控制器view的frame
	//        navigationController.view.frame = CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height - kTabbarHeight);
	        
	        navigationController.interactivePopGestureRecognizer.delegate = nav.popDelegate;
	        // 让Dock从root上移除
	        [_mytabbar removeFromSuperview];
	 
	        //_mytabbar添加dock到HomeViewController
	        _mytabbar.frame = self.tabBar.frame;
	        [self.view addSubview:_mytabbar];
	    }
	}
	```

	* 3. 自定制tabbar上的button，接受用户点击事件，然后通过代理，传递到TabBarController，最终调用：[super setSelectedIndex:index];，控制其他controller！！！

	* 4. 最后，中间凸起的样式如何实现？是tabbar的背景图，然后中间的大button的背景图，两者契合，即可达到效果！
	```
	tabBar.backgroundColor = [UIColor colorWithPatternImage:[UIImage imageNamed:@"tab_background"]];

	...

	UIView *bgView = [[UIView alloc] init];
    bgView.backgroundColor = [UIColor colorWithPatternImage:[UIImage imageNamed:@"tab_Irregular"]];
    [self insertSubview:bgView atIndex:0]; // self is BXTabBarBigButton
	```

	* 5. 同样，超出tabbar的区域点击，也需要重载- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event

### [iTofu/LCTabBarController](https://github.com/iTofu/LCTabBarController)

