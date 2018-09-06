## Carthage 熟悉

[如何使用Carthage管理iOS依赖库]()

### 安装Carthage

```
$ brew update

$ brew install carthage
```

也可以从网站https://github.com/Carthage/Carthage/releases下载最新版的Carthage.pkg来更新。

### 使用Carthage

1. 查看版本：carthage version

2. 集成

  * 通过终端进入到项目所在的文件夹：cd ~/Path/Project
  * 创建一个空的carthage文件：touch Cartfile
  * 使用xcode打开cartfile文件：open -a Xcode Cartfile
     > github "Alamofire/Alamofire" ~> 3.0  
     > github "SwiftyJSON/SwiftyJSON"
     >> ~> 3.0 表示使用版本3.0以上但是低于4.0的最新版本，如3.5, 3.9
     >> == 3.0 表示使用3.0版本
     >> >= 3.0表示使用3.0或更高的版本
     >> 如果你没有指明版本号，则会自动使用最新的版本

  * 保存并关闭cart file文件，在终端执行命令：carthage update --platform iOS
