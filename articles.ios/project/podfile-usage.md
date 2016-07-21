## CocoPods


## Option 'use_frameworks!'
http://blog.cocoapods.org/CocoaPods-0.36/: use_frameworks! enables Swift support by building your code in the new dynamic frameworks. Dynamic frameworks only support iOS 8

##  [CocoaPods pod install/pod update更新慢的问题](http://blog.csdn.net/ralbatr/article/details/39082937)

无论是执行pod install还是pod update都卡在了Analyzing dependencies不动：原因在于当执行以上两个命令的时候会升级CocoaPods的spec仓库，加一个参数可以省略这一步，然后速度就会提升不少。加参数的命令如下：
pod install --verbose --no-repo-update
pod update --verbose --no-repo-update
