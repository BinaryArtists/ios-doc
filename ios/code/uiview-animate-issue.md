# +[UIView animateWithDuration:animations:completion:] 内部大概是如何实现的？

```
animateWithDuration:这就等于创建一个定时器
animations:这是创建定时器需要实现的SEL
completion:是定时器结束以后的一个回调block
```

## 什么时候会发生「隐式动画」？

当改变CALayer的一个可做动画的属性，它并不能立刻在屏幕上体现出来.相反，它是从先前的值平滑过渡到新的值。这一切都是默认的行为，你不需要做额外的操作,这就是隐式动画

## 