## 讲讲库：framework、static library、dynamic library

参考链接：
[iOS的主要框架介绍](http://blog.csdn.net/yuhuangc/article/details/7575519)

### 目录
* [1. 什么是框架（framework)？](#1)
* [2. ]()

<h3 id="1">什么是框架（framework)？</h3>

1. 是什么？
     框架是一个目录，这个目录包含了共享库，访问共享库里代码的头文件，和其它的图片和声音的资源文件。一个共享库定义的方法或函数可以被应用程序调用。

2. 怎么用？
    * Build Phases -> Link Binary With Libraries 中添加
    * 要使用一个框架，需要将它添加到你的项目中，你的项目才可以使用它。许多应用程序都使用了如Foundation、UIKit、和Core Graphics这些框架。根据你为应用程序选择的模版，相关的框架就已经被自动引入了。如果默认加入的框架不能满足你的应用程序的需求，你也可以加入需要的框架。

<h3 id="2"></h3>

<h3 id="3"></h3>

<h3 id="4"></h3>

<h3 id="5"></h3>

<h3 id="6"></h3>
