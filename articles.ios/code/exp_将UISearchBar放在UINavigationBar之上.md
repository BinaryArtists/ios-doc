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
