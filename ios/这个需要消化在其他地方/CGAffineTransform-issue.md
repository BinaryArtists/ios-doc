## CGAffineTransform

### CGAffineTransformMakeScale

两个参数，代表x和y方向缩放倍数

### CGAffineTransformScale

三个参数，第一个为要进行变换的矩阵，二三为x和y方向缩放倍数

### CGAffineTransformMakeScale vs CGAffineTransformScale

CGAffineTransformMakeScale是对单位矩阵进行缩放。
CGAffineTransformScale是对第一个参数的矩阵进行缩放。

比如已经对一个view缩放0.5，还想在这个基础上继续缩放0.5，那么就把这个view.transform作为第一个参数传到CGAffineTransformScale里面，缩放之后的view则变为0.25(CGAffineTransformScale(view.transofrm,0.5,0.5))。如果用CGAffineTransformMakeScale方法，那么这个view仍旧是缩放0.5(CGAffineTransformMakeScale(0.5,0.5))。

另外，想要将两个transform的属性都改变的话，需要这样:
```
alertView.transform = CGAffineTransformMakeScale(.25, .25);
alertView.transform = CGAffineTransformTranslate(alertView.transform, 0, 600);
```
或者:
```
CGAffineTransform viewTransform = CGAffineTransformConcat(
CGAffineTransformMakeScale(.25, .25), CGAffineTransformMakeTranslation(0, 600));
alertView.transform = viewTransform;
```