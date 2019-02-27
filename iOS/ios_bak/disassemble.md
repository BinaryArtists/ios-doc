## 反编译

  1. [ios逆向工程-静态分析](http://my.oschina.net/iq19900204/blog/340297)
    > 拿个越狱机-下好工具
    > 去越狱平台下个想分析的应用（或者去app store下，用解密工具解密一下）
    > 导入reveal分析页面，得到想要的知道的具体视图类或者大致范围
    > 分析class-dump中，找到想要的类和函数
    > 在IDA中找到具体函数，查看逻辑

### class-dump使用

  * 用于简单分析出工程中的类名和函数名

  * 简书的[使用class-dump导出其他应用头文件](http://www.jianshu.com/p/6a6ce18f998e)

  * 从App Store下载的App都是经过加密签名的，相当于有一层“壳”，class-dump应付不了这样的文件

### AppCrackr 砸壳

如果你能找到在高级OSX系统中使用的版本，类似的如：Clutch，Crackulous，[纯UI方式？？](http://blog.csdn.net/oyzhx/article/details/38339129)

### dumpdecrypted使用

[《iOS应用逆向工程》学习笔记（六）使用dumpdecrypted砸壳](http://www.2cto.com/kf/201408/322905.html)

### Hopper Disassembler 使用

  * 类似IDA，能简单转换成OC的功能

### IDA 使用

  * 强大的反编译工具

### reveal

  * UI层解析工具
