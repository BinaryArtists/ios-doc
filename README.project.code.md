项目中的细节，客官请往下看

参考链接：
1. http://www.bubuko.com/infodetail-397875.html
2. https://www.baidu.com/s?wd=iOS%20%E5%BC%80%E5%8F%91%E6%80%BB%E7%BB%93%E4%B9%8B&rsv_spt=1&rsv_iqid=0xfd71a6d2000594dd&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&tn=baiduhome_pg&rsv_enter=1&oq=%25E4%25B9%259D%25E5%25B1%2582%25E5%25A6%2596%25E5%25A1%2594%2520%25E7%2594%25B5%25E5%25BD%25B1&rsv_t=aa99TxtL8EvW6cmjLVufH93%2FbJjlY6nu51uNP%2B%2By41hd9SRVHW0rkTi%2FFGwTD%2BwSb46M&sug=%E4%B9%9D%E5%B1%82%E5%A6%96%E5%A1%94%20%E7%94%B5%E5%BD%B1&rsv_pq=9c9588aa00075f3a&rsv_sug3=36&rsv_sug1=21&rsv_sug2=0&inputT=336703&rsv_sug4=336873

## 基类
	* 首先请问问自己，是否需要基类，组合、类别亦可实现代码下沉以致于很好重用，表配置与查询可以实现多态等等。
	* 我们使用了基类：(project/code中有部分案例文件)
	```objc
		@class BaseViewController;
			@impl 导航栏风格
			@impl 部分常用方法的统一命名
		@class BaseViewModel;
		@class BaseModel;
	```

	* 还使用了协议：
	```objc
		@protocol BaseViewProtocol;
		typedef BaseViewProtocol ReactiveProtocol;
	```

## 特殊控件 和 通用控件
	* 警告框
		1. 常用其block实现的版本