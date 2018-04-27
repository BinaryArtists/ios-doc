# XCode

## Build Settings

### Build Options 

* [Debug Information Format](http://blog.csdn.net/longzs/article/details/51272980)
	- DWARF with dSYM File 		会生成 dSYM 文件
	- DWARF						不会生成 dSYM 文件


## 编辑 xcodeproj

1. https://draveness.me/bei-xcodeproj-keng-de-zhe-ji-tian
2. http://www.jianshu.com/p/98029cf49a69
3. https://github.com/CocoaPods/Xcodeproj

## 搜索技巧

1. 正则表达式搜索IP：
```
(?<![\.\d])(?:\d{1,3}\.){3}\d{1,3}(?![\.\d])
```


## 问题集
### [iOS:升级Xcode9.3后cocoapods update 失败](https://www.jianshu.com/p/bd7afe1c5ea4)

修改XCode工程Project下的配置：Project Document -> Project Format -> 切换版本