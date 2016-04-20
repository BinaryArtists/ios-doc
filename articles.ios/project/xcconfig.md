## xcconfig 使用

  * [Xcconfig简单使用指南](http://www.jianshu.com/p/1a542dc3b34a)

### xcconfig 写法

  * [Xcconfig变量设置说明](http://www.jianshu.com/p/44c82630bd50)

### xcconfig 配置

  * 点击工程文件，Info -> Configurations，初始值为 No Configurations Set

  * 细节事项
    > 一个xcconfig可以对应多个TARGET
    > ConfigDemo对应PROJECT
    > Debug，RELEASE对应TARGETS
    > 对应TARGETS的xcconfig优先级大于对应PROJECT的。在这里（Debug>ConfigDemo），即如果Debug.xcconfig设置的参数，ConfigDemo.xcconfig也设置了该参数，则使用Debug.xcconfig设置的参数

    > 若要修改Build Settings 里面的功能项直接选中要修改行复制，粘帖到xcconfig文件当中。每次复制会有重复的东西，删除即可。
