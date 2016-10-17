## [深入理解Objective-C的Block](http://www.molotang.com/articles/1691.html)

参考：
	* [深究Block的实现](http://www.cocoachina.com/ios/20160225/15441.html)

### block跟似乎C语言的函数指针是一样的，但是两者仍然存在以下区别：

	1. block的代码是内联的，效率高于函数调用
	2. block对于外部变量默认是只读属性
	3. block被Objective-C看成是对象处理

### block的申明和定义

[block-1](https://github.com/BinaryArtists/not-just-code/blob/master/ios/imges/block-1.jpg)

### summary

block捕获变量、代码传递、代码内联等特性赋予了它多于代理机制的功能和灵活性，尽管它也存在循环引用、不易调试追溯等缺陷，但无可置疑它的优点深受码农们的喜爱。如何更加灵活的使用block需要我们对它不断的使用、探究了解才能完成。[iOS开发-由浅至深学习block](http://www.cocoachina.com/ios/20160414/15921.html)
