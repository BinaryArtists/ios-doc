## 有关于UIScrollView的contentSize计算

### 参考
1. 链接：[示例详解：UIScrollview 与 Autolayout 的那点事](http://www.cocoachina.com/ios/20151221/14757.html)
2. 原文件：[MASExampleScrollView.m](https://github.com/SnapKit/Masonry/blob/master/Examples/Masonry%20iOS%20Examples/MASExampleScrollView.m)

### 澄清
1. 首先参考的示例，描述并不准确。。。。。。。。。？？？？


## 原理
1. 注意：“UIScrollView是个非常特殊的view UIScrollView与其subview之间相对位置的约束 并不会直接用于frame的计算 而是会转化为对ContentSize的计算”（怎么会用于frame呢？父view和子view，视图层次不同，不能同日而语）
2. 如参考链接中，如果再Scollview上直接添加多个view，并添加正确的约束，scrollView的contentSize会是这些子view“并集”矩形。

## 静态设定内容
在xib中，添加UIScrollView，然后再为它添加子View，为contentview，直接约束定好纵向或横向content长度。代码上直接add展示view就行了。

## 动态加载内容
看masonry中如何添加contentView的：
```objc
UIView* contentView = UIView.new;
    [self.scrollView addSubview:contentView];
    
    [contentView makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(self.scrollView);
        make.width.equalTo(self.scrollView);
    }];
```

再来看看，怎么在contentView上面添加实际内容view的：
```objc
UIView *lastView;
    CGFloat height = 25;
    
    for (int i = 0; i < 10; i++) {
        UIView *view = UIView.new;
        view.backgroundColor = [self randomColor];
        [contentView addSubview:view];
        
        UITapGestureRecognizer *singleTap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(singleTap:)];
        [view addGestureRecognizer:singleTap];
        
        [view mas_makeConstraints:^(MASConstraintMaker *make) {
            make.top.equalTo(lastView ? lastView.bottom : @0);
            make.left.equalTo(@0);
            make.width.equalTo(contentView.width);
            make.height.equalTo(@(height));
        }];
        
        height += 25;
        lastView = view;
    }
    
    [contentView makeConstraints:^(MASConstraintMaker *make) {
        make.bottom.equalTo(lastView.bottom);
    }];
```
要注意最后一句～
