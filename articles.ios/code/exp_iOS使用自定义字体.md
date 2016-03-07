## [iOS使用自定义字体](http://blog.csdn.net/heartofthesea/article/details/22289399)


1. 添加到工程
2. 确保对应target的build phase－copt bundle resources中有文件
3. 在info.plist中添加Fonts provided by application（item 0，item 1等等）添加一项，对应新增字体文件
4. 在Mac下双击字体文件，在标题栏中找到字体的fontName
5. 打印出系统所有的字体名，查找到4中的字体名（这个才是真正需要的）
6. 这样就可以使用了[UIFont fontWithName:@"chenweixun-yingxing" size:20];
