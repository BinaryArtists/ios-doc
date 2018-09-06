## UITableView的headerView、footerView的粘性（sticky）

* [如果允许，可以将 cell 当footerView来使用。需要显示footerView的地方，把Cell的高度设置为footerView的高度，即可。](http://www.cocoachina.com/bbs/read.php?tid=272286)
* [在-(void)scrollViewDidScroll:(UIScrollView *)scrollView中做文章](http://blog.csdn.net/hopedark/article/details/39226049)

	```objc
	//去掉UItableview headerview黏性(sticky)  
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {  
    CGFloat sectionHeaderHeight = 40;  
    if (scrollView.contentOffset.y<=sectionHeaderHeight&&scrollView.contentOffset.y>=0) {  
        scrollView.contentInset = UIEdgeInsetsMake(-scrollView.contentOffset.y, 0, 0, 0);  
    } else if (scrollView.contentOffset.y>=sectionHeaderHeight) {  
        scrollView.contentInset = UIEdgeInsetsMake(-sectionHeaderHeight, 0, 0, 0);  
    }  
}
	```
* 还有一种方法蛮好（这样实际上是把table向上移动了一部分隐藏在navigation下面）

	```objc
	_mainTable.contentInset = UIEdgeInsetsMake(sectionHeight, 0, 0, 0); 
	```
* 还有一种案例是：键盘弹起的时候，会将footerView推上去，怎么解决呢？看下面例子代码，就懂了。
```objc
if ([notification is:UIKeyboardWillHideNotification]) {
		UIEdgeInsets contentInsets = UIEdgeInsetsZero;
        self.tableView.contentInset = contentInsets;
        } else if ([notification is:UIKeyboardWillShowNotification]) {
        NSDictionary *userInfo = [notification userInfo];
        CGSize keyboardSize = [[userInfo objectForKey:UIKeyboardFrameBeginUserInfoKey] CGRectValue].size;
        UIEdgeInsets contentInsets = UIEdgeInsetsMake(0.0, 0.0, -keyboardSize.height, 0.0);
        self.tableView.contentInset = contentInsets;
    }
```
