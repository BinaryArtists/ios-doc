## viewDidAppear不调用？什么时候调用

苹果的文档是这样描述的：

If the view belonging to a view controlleris added to a view hierarchy directly, the view controller will not receivethis message. If you insert or add a view to the view hierarchy, and it has aview controller, you should send the associated view controller this messagedirectly. Failing to send the view controller this message will prevent anyassociated animation from being displayed.

如果是直接把View Controller的view addSubview到另一个View，则不会调用viewDidAppear，你需要手动发送viewDidAppear给这个View Controller。

那么我们是不是可以这样认为：
pushViewController,或presentModelViewController这个View Controller时，会调用它的viewDidAppear方法呢？苹果文档中未说明。

事实上即使是用presentModelviewController，viewDidAppear也不一定能被调用。测试的结果表明：如果nib第1次加载的时候，viewDidAppear一定会调用的。但如果第2次加载这个nib则不一定会调用。viewDidLoad则无论何种情况总是调用的，所以要保险得多。另外，在iPad中没有这样的问题，viewDidAppear总是会调用的。



 补充说明，viewWillAppear和viewDidAppear的情况是一样的，在iPhone上，除了第1次加载xib文件时肯定会调用外，其他任何情况都不会自动调用：无论popViewControllerAnimate、presentModalViewController还是addSubviews。但iPad上则相反，除了addSubviews方法外，都一定会调用。此外，测试是在ios4.3设备上进行的。

btw:viewDidLoad方法只有在initWithNibName时会调用。



如果ViewController是在TabViewController中加载的，则等同于addSubView。当TabViewController第一次显示时，这个ViewController的viewDidLoad方法会调用，而viewDidAppear方法不会调用。但是当点击TabBarItem时则恰巧相反，只会调用ViewController的viewDidAppear而不会调用viewDidLoad。

