## UIApplication的详细介绍

参考：[UIApplication的详细介绍](http://www.jianshu.com/p/a72bcb948371)

### UIApplication

* 什么是UIApplication?

UIApplication对象是应⽤程序的象征.
每一个应用都有⾃己的UIApplication对象,这个对象是系统自动帮我们创建的, 它是一个单例对象.
一个iOS程序启动后创建的第一个对象就是UIApplication对象
我们只能通过[UIApplication sharedApplication]获得这个单例对象,不能够手动去创建它.

* UIApplication作用?

利⽤用UIApplication对象,能进⾏一些应用级别的操作.
可以设置应用程序图标右上角的红色提醒数字
设置联⽹指示器的可见性
可以设置应用程序的状态栏，进行应用之间的跳转.

### 模仿UIApplication单例.
