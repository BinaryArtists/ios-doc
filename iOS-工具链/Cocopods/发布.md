# Cocopods 发布

[CocoaPods详解之----制作篇](http://blog.csdn.net/wzzvictory/article/details/20067595)
[如何发布自己的开源框架到CocoaPods](http://blog.csdn.net/blog_t/article/details/59570385)
[使用Cocoapods创建私有podspec](http://blog.wtlucky.com/blog/2015/02/26/create-private-podspec/)

## 过程

1. 注册trunk

```
// pod trunk register EMAIL [NAME], 注册成功后会在你的邮箱收到一份确认邮件
pod trunk register fengzilijie@qq.com 'fallenink' --verbose

// 注册成功后可以看到相应的信息
pod trunk me
```

2. 创建podspec

```
// 用pod的命令创建podspec文件
pod spec create oc-foundation
```

*说明：*
A s.summary 即pod search 'name'的时候显示的说明
B s.homepage git的仓库地址
C s.license Pods依赖库使用的license类型，填上对应的即可
D s.source_files 源文件路径，相对于podspec文件而言，本文中是DHCLLocation的文件夹下面的文件
E s.frameworks 需要用到的frameworks，上图我截图时候截掉了，这里补充下
```
*1   s.frameworks="CoreLocation","Foundation"
```
F. “*” 表示匹配所有文件 “*.{h,m}” 表示匹配所有以.h和.m结尾的文件 “**” 表示匹配所有子目录
```
“ECGCustomAlertView/*” 
“ECGCustomAlertView/ECGCustomAlertView/*.{h,m}” 
“ECGCustomAlertView/*/.h”
```

3. 通过trunk推送podspec文件, 以及验证pod文件

*前置条件*
①.确保你的源码已经push到Github上。如果还没push源代码，可以用Terminal cd到本地源代码的根目录，执行
```
git add -A

git commit -m"first commit for version 1.0.0"

git push origin master
```

②.确保你所push的代码已经打上"version tag"，也就是给源代码打上版本号标签：
```
git commit -m "Release 1.0.1"  (先提交当前修改)
git tag 1.0.1            (添加tag)
git push --tags         (推送tag到远程)
git push origin master  (推送到远程到代码仓库)
```
确认上面的两点后，为了让推送能够顺利进行，我们可以对我们的pod文件进行一个验证。

3. 提交之前先验证.podspec文件是否合法
```
pod spec lint oc-foundation.podspec  
pod spec lint oc-foundation.podspec --allow-warnings     (忽略警告)
pod spec lint oc-foundation.podspec --verbose
```

```
pod lib lint
```

3.1 本地测试podspec文件

我们可以创建一个新的项目，在这个项目的Podfile文件中直接指定刚才创建编辑好的podspec文件，看是否可用。 在Podfile中我们可以这样编辑，有两种方式

```
platform :ios, '8.0'

pod 'PodTestLibrary', :path => '~/code/Cocoapods/podTest/PodTestLibrary'      # 指定路径
pod 'PodTestLibrary', :podspec => '~/code/Cocoapods/podTest/PodTestLibrary/PodTestLibrary.podspec'  # 指定podspec文件
```

然后执行pod install命令安装依赖，打开项目工程，可以看到库文件都被加载到Pods子项目中了，不过它们并没有在Pods目录下，而是跟测试项目一样存在于Development Pods/PodTestLibrary中，这是因为我们是在本地测试，而没有把podspec文件添加到Spec Repo中的缘故。

在项目中编写代码，测试库文件无误后就可以开始下一步了，提交podspec到Spec Repo中。

4. 验证.podspec文件报错，解决方法

* 先删除tag
```
// 删除本地tag
git tag -d 1.0.0
// 删除远程tag
git push origin -d tag 1.0.0
```
* 修改spec文件（必须修改相应的version和source）
* 重新执行–>设置tag号，提交修改的步骤

5. 向Spec Repo提交podspec

向Spec Repo提交podspec需要完成两点一个是podspec必须通过验证无误，在一个就是删掉无用的注释（这个不是必须的，为了规范还是删掉吧）。 向我们的私有Spec Repo提交podspec只需要一个命令

```
$ pod repo push WTSpecs PodTestLibrary.podspec  #前面是本地Repo名字 后面是podspec名字
```

完成之后这个组件库就添加到我们的私有Spec Repo中了，可以进入到~/.cocoapods/repos/WTSpecs目录下查看

```
.
├── LICENSE
├── PodTestLibrary
│   └── 0.1.0
│       └── PodTestLibrary.podspec
└── README.md
```


再去看我们的Spec Repo远端仓库，也有了一次提交，这个podspec也已经被Push上去了。

至此，我们的这个组件库就已经制作添加完成了，使用pod search命令就可以查到我们自己的库了

```
$ pod search PodTestLibrary

-> PodTestLibrary (0.1.0)
   Just Testing.
   pod 'PodTestLibrary', '~> 0.1.0'
   - Homepage: https://coding.net/u/wtlucky/p/podTestLibrary
   - Source:   https://coding.net/wtlucky/podTestLibrary.git
   - Versions: 0.1.0 [WTSpecs repo]
```

这里说的是添加到私有的Repo，如果要添加到Cocoapods的官方库了，可以使用trunk工具，具体可以查看官方文档