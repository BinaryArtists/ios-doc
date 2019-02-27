# 宏定义 ‘注入’ 的几种方法

### 在代码源文件中直接定义

直接在文件中用  #define ... 来定义：此方式优先级最高，当用多种方式宏定义一个变量时，这种方式为最后的值

### 在 target 的 Build Setting中定义

在Build Settings中的Apple LLVM 7.0(或者其他版本号) - Preprocessing->Preprocessor Macros中定义：和第3种方式平级

### 在xcodebuild打包中定义

用xcodebuild打包，在打包命令行中使用
xcodebuild -project "${BUILDPROJECT}.xcodeproj" -target $BUILDPROJECT  GCC_PREPROCESSOR_DEFINITIONS='${inherited} LOGONE=1 LOGTWO=1' build
其中红色为宏定义LOGONE和LOGTWO

其中如果不加${inherited}的话，编译会报错，说是重复定义，加上了以后第3种方式宏定义的优先级就高于了2，当分别用2和3定义一个变量的时候，3定义为最后的值，但是加了以后仍然优先级低于第一种方式。

### 使用xcconfig文件

[Xcode使用xcconfig文件配置环境](http://blog.csdn.net/caryaliu/article/details/51534511)