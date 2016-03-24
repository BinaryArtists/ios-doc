## UIView在viewDidLoad中设置center.x无效

网络上其他人也遇到这类问题：
1. [viewDidLoad中修改center.x无效](http://www.imooc.com/qadetail/109641)
2. [ ios 通过设置UI控件的center和size来设置位置时需注意！](http://blog.csdn.net/make___/article/details/46534477)
3. 单独设置setCenterY，也是同理。

### 问题陈述

利用frame来布局的时候，我们常常会使用UIView+Hierarchy类别来控制，
此时如果在viewDidLoad中设置center.x，会无效。

### 解决方法

1. 换作使用约束
2. 在viewWillAppear或者viewDidAppear中去修改frame
3. 经测试：此时同时设置center.x center.y也会OK！（所以按推荐的来，要设置位置，都用center，要么就用frame）

### Why

1. 如果要通过设置center和frame.size来控制控件的位置，只有先设置frame.size再设置center，才能正确设置位置。
2. frame、bounds和center的使用场景
  * 一般来说，bounds属性比较少用，通常使用frame来设置视图的大小和位置，使用center来改变（移动）视图的位置(也可用frame来改变位置）。另外，对视图进行旋转、缩放也都是相对于center进行操作的。
From: http://unmi.cc/ios-bounds-frame

  * frame和bounds是UIView中的两个属性(property)。
    > frame指的是：该view在父view坐标系统中的位置和大小。（参照点是父亲的坐标系统）
    > bounds指的是：该view在本身坐标系统中 的位置和大小。（参照点是本身坐标系统）
