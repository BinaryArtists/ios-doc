






[节选自哪里？？？](？？？？)
只要你提前告诉CoreAnimation你要渲染的View的形状Shape,就会减少离屏渲染计算

[myView.layer setShadowPath：[[UIBezierPath
    bezierPathWithRect：myView.bounds] CGPath];
加上这行代码，就减少离屏渲染时间，大大提高了性能
