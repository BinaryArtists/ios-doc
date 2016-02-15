## 细枝末节

## ？？？

1. 等号两边的表达式，谁先执行？

```objc
UIView *view1 = (UIView *)self.description;
UIView *view = (UIView *)self; // not UIView
view.width = view1.width;

view1部分先导致crash
```
