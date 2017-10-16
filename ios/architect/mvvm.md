## mvvm

请先看：[mvc](mvc.md)

## 结构与分工

### 视图

* 原则
	- 高度计算，不放在view

### 视图模型 

* 责任
	- 数据加工（到什么程度？通过数据绑定，可以让UI直接响应）
	- 高度计算
	- 对应视图的数据模型 （ViewDataSource）

* 原则
	- 由视图帮承担，数据绑定的责任；视图模型，只有与视图映射的视图模型

### 控制器

* 责任
	- 事件绑定 （ViewDelegate）
	- 页面路由
	- 必要的视图逻辑（动画、切换）

* 原则
	- ？？？

### 数据模型

* 责任
	- 业务逻辑
	- 数据获取
	- 数据持久化

可见这一层责任还是很重的，请移步[mvc](mvc.md)，做更多思考

### 结构

1. ViewModel与Controller一对一 （比较中规中矩，ViewModel纯是给Controller减负）

* AAAViewController
	- AAAViewModel
		- [AAACellModel]
	- AAACell
		- AAACellModel (这是贫血模型，不是视图模型)
	- AAAView
* AAAModel

2. ViewModel与View一对一，同时与Controller一对一 （比较深度使用，ViewModel与View组合，更容易复用）

* AAAViewController
	- AAAViewModel
		- [AAACellModel]
		- [AAAViewModel]
	- AAACell
		- AAACellModel
	- AAAView
		- AAAViewModel
* AAAModel

## 其他案例分析

### RAC 的使用

* 责任
	- 数据绑定
	- 事件绑定
	- 数据流处理
	- 多状态依赖处理
	- accessor 可以使用链式，但不推荐使用RAC


## 参考

1. [MVVM模式的简单通俗理解](http://www.open-open.com/lib/view/open1483067348480.html)