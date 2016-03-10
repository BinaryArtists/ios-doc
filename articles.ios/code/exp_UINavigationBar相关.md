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

  * 终极方案：不用系统的NavigationBar啦，咱们自定义一个
