## [将UISearchBar放在UINavigationBar之上](http://www.gowhich.com/blog/149)

```objc

- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    self.navigationController.navigationBar.tintColor = [UIColor redColor];

    //添加左侧的item
    UIBarButtonItem *leftItem = [[UIBarButtonItem alloc] initWithTitle:@"艺人"
                                                                 style:UIBarButtonItemStyleBordered target:self
                                                                action:@selector(showSelect)];
    self.navigationItem.leftBarButtonItem = leftItem;


    //导航条的搜索条
    _searchBar = [[UISearchBar alloc]initWithFrame:CGRectMake(0.0f,0.0f,255.0f,44.0f)];
    _searchBar.delegate = self;
    [_searchBar setTintColor:[UIColor redColor]];
    [_searchBar setPlaceholder:@"输入艺人名字"];

    //将搜索条放在一个UIView上
    UIView *searchView = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 768, 44)];
    searchView.backgroundColor = [UIColor blueColor];
    [searchView addSubview:_searchBar];
    self.navigationItem.titleView = searchView;

    //设置tableview的代理
    self.dataTable.delegate = self;
    self.dataTable.dataSource = self;

    //加载数据
    NSString *path=[[NSBundle mainBundle] pathForResource:@"category" ofType:@"plist"];
    self.dataDic = [[NSMutableDictionary alloc] initWithContentsOfFile:path];
}

```

如果需要调整searchBar的位置

```objc
  _searchBar = [[UISearchBar alloc]initWithFrame:CGRectMake(0.0f,0.0f,255.0f,44.0f)];
```


开发过程中，发现titleview很难居中，通过各种尝试终于找到了解决方法。
首先清楚你个概念：
1.leftBarButtonItem，导航条中左侧button。
2.rightBarButtonItem，导航条中右侧button。
3.titleview，不用介绍了吧，就是标题。
问题原因：
经过尝试，发现titleview的起点位置和尺寸依赖于leftBarButtonItem和rightBarButtonItem的位置。
解决方案：
设置titleview之前，先初始化leftBarButtonItem和rightBarButtonItem的位置，
然后根据leftBarButtonItem和rightBarButtonItem的位置来使titleview居中。


*注意*
最后：如果是在xib中拖的titleView，并自动布局
1. 需要contentView，设置约束与titleView一致
2. 其他约束也需要格外注意！
