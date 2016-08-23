## 创建横向的UITableView

todo
1. 实现一个HorizontalTableView，舍弃当前方案, [thevole/HorizontalTable](https://github.com/thevole/HorizontalTable/blob/master/Classes/HorizontalTableView.h)，[alekseyn/EasyTableView](https://github.com/alekseyn/EasyTableView)
2. tableview 优化：[johnil/VVeboTableViewDemo](https://github.com/johnil/VVeboTableViewDemo)

### 以下来自：[创建横向的UITableView](http://blog.csdn.net/sodaslay/article/details/12105873)

UITableView只支持竖向显示，要实现横向的显示，可以设置tableview的transform属性为CGAffineTransformMakeRotate（-M_PI/2),

即逆时针旋转90°，然后对所有的cell设置transform属性为CGAffineTransformMakeRotate（M_PI/2),即顺时针转90°，这样所有的cell内容即可正确的横向显示。对于设置坐标的技巧，可以在设置完transform属性后，在进行坐标的设置，若在之前设置，坐标会被transform属性旋转而得不到正确的位置。

cell的宽度为heightforrowatindexpath返回值，但是其高度为tableview的宽度（tableview的frame的width），这应该是系统自动设定。所以cell的高度可能非常大，导致contentview显示出现问题。解决的方案可以再cell的layoutsubviews里重新设定cell的frame为指定大小。

如果需要添加tableheaderview等，需要将该view也转π/2才可正确显示，此view的坐标和view上的subview坐标起始位置同cell。而且，调整了tableview的frame以后，如果reloaddata可能会造成headerview宽度变为tableview的宽度导致不能正确显示（与cell非常相似），需要再次调整view的frame才可。详细的情况可以通过tableview的subviews查看该view的frame。


### 我的尝试

[UITableView+Extension](https://github.com/BinaryArtists/suite.oc/blob/master/mvc-native/View/TableView/UITableView%2BExtension.h)

```
/**
 *  使用该方法
    1. 不能使用自动约束
    2. UITableView 要代码创建
 
 *  Usage:
 
    1. UITableView 设置
 
        CGRect tableViewRect = CGRectMake(0, 0, OrderTimeSelectCellHeight, screen_width);
        self.tableView = [[UITableView alloc] initWithFrame:tableViewRect style:UITableViewStylePlain];
        [self.contentView addSubview:self.tableView];
        [self.tableView normally];
        self.tableView.frame = CGRectMake(0, 0, OrderTimeSelectCellHeight, screen_width);
        self.tableView.center = CGPointMake(screen_width / 2, OrderTimeSelectCellHeight / 2);
        self.tableView.delegate = self;
        self.tableView.dataSource = self;
        self.tableView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleLeftMargin | UIViewAutoresizingFlexibleRightMargin;
        [self.tableView rotateToHorizontalScrollable];
        [self.tableView noneSeparator];
        [self.tableView setFixedRowHeight:83.f];
        [self.tableView registerNib:[TimeSelectCell nib] forCellReuseIdentifier:[TimeSelectCell identifier]];
 
    2. UITableView 的delegate、dataSource
 
        - (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
            return 1;
        }
        - (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
            return 14;
        }
        - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
            TimeSelectCell *cell = [tableView dequeueReusableCellWithIdentifier:[TimeSelectCell identifier] forIndexPath:indexPath];
            [cell noneSelection];
            [cell setModel:nil];
            return cell;
        }
        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            [self.delegate onSelectDate:@"20160910:1400"];
        }
 
    3. UITableViewCell 设置
 
        3.1 UITableViewCell 代码创建
            - (UITableViewCell *)tableView :( UITableView *)aTableView cellForRowAtIndexPath :(NSIndexPath *)indexPath {
                UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"identifier"];
                if (cell == nil) {
                    cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier@"identifier"];
         
                // cell顺时针旋转90度
                [cell rotateToHorizontalScrollable]; // 注意！！！
            }
         
            return cell;
            }
     
        3.2 UITableViewCell Xib创建
     
            - (void)awakeFromNib {
                [super awakeFromNib];
                [self rotateToHorizontalScrollable];
            }
         
            - (void)layoutSubviews {
                [super layoutSubviews];
                self.frame = CGRectMake(self.frame.origin.x, self.frame.origin.y, cell宽度, cell高度); // http://www.360doc.com/content/13/0930/15/8772388_318187088.shtml
            }
 */
```

### 推荐一个采用该方案实现的可服用控件，一个日期选择控件

[m1entus/MZDayPicker](https://github.com/m1entus/MZDayPicker)

### 最后：不推荐使用该方案，比较琐碎