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


## 最后 额外阐述 当前项目中结构

### 主要构成对象

View、ViewService、ViewController、DataService、Model

### 框图


### 责任

* View
	- DataSource protocol impl (其实是 数据源协议 的实现)
	- 
* ViewService
	- DataSource data（其实是 data ）
	- Data binding
	- View logic
* ViewController
	- Event binding
* DataService
	- 面向view的DS：HomeDataService
	- 面向model的DS：UserDataService，且面向view和面向data是上下层关系，不可逆向依赖
	- 技巧：
		> 如果要服用DataService，则拆分
* Model、Entity


### 例子（文件夹例子）

---
* Base
	- BaseView
	- BaseViewService (XXXViewService or XXXVS)
	- BaseViewController (XXXViewController or XXXVC)
	- BaseDataService (XXXDataService or XXXDS)
	- BaseModel, BaseEntity
* Bean
	- User
	- Book
* Module
	- Home
		> View
		> ViewService
		> ViewController
		> DataService
		> Model
		> Entity
	- BookList
* Manager
	- PayManager
	- ShareManager
	- LocationManager
	- SessionManager：管理应用的临时信息

---
* App
	- TrainTicketApp
		> Module
		> Manager
	- HotelTicketApp
	- BusTicketApp


## 参考

1. [MVVM模式的简单通俗理解](http://www.open-open.com/lib/view/open1483067348480.html)