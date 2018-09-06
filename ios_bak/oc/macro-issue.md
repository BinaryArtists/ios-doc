## 宏

参考：
1. [iOS中打印日志显示系统详细时间，类名，行号及打印值](http://blog.csdn.net/liulushi_1988/article/details/8500576)

### 预编译宏

宏	       说明
__func__	打印当前函数或方法，c字符串
__LINE__	打印当前行号，整数
__FILE__	打印当前文件路径，c字符串
__PRETTY_FUNCTION__	打印当前函数或方法（在C++中会包含参数类型），c字符串


### 在运行时动态打印一个类名，消息名，当前堆栈信息

代码	                             说明
NSStringFromSelector(SEL) 	      获取selector的名字
NSStringFromSelector(_cmd_)	      获取当前方法名
NSStringFromClass([object class])	获取object的类名
NSThread callStackSymbols]	      获取当前线程的栈，是一个NSArry，包含堆栈中所有函数名
