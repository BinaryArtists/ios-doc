# XCode9

* [XCode9的新变化](http://www.cnblogs.com/lurenq/archive/2017/09/26/7594909.html)

### 折叠代码

### 关于警告

我们定义一个不带参数的block,通常是如下的方式
```
typedefvoid (^UpdateSwichBtnBlock)();
```
在xcode9中会提示一个警告
```
This block declaration is not a prototype
Insert ‘void'
```

解决方式可以是如下的几种
```
typedefvoid (^UpdateSwichBtnBlock)(void);
```
但是这样,很多第三方要改,涉及的面太大了,目前可能不太适合,虽然这个是趋势.

或者,如果只是很少的地方,也可以使用
```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wstrict-prototypes"
typedefvoid (^UpdateSwichBtnBlock)();
#pragma clang diagnostic pop
```
彻底的暂时解决所有这种警告的方式

在工程的设置中
```
Strict Prototypes -> NO
```
设置为NO,则这些警告就消失了

不过这不是一种好习惯,只是暂时性的不让提示这种类型的警告而已

### 重构相关

rename, 

### cmd +/- 可以用来调整编辑器的字体大小了

### Source Control的极大增强


### 新的构建系统

### crash可以直接在本机上看到

### runtime问题检测

### 模拟器可以多开了,并且,模拟器可以登录

### 无线开发

###  Folder和Group的同步性