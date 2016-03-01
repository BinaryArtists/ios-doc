## [提 APPStore 报 Invalid Swift Support 有人遇到过么？](http://swiftcn.io/topics/17)

参考链接：
	1. [Invalid Swift Support / The SwiftSupport folder is empty](http://stackoverflow.com/questions/32559204/invalid-swift-support-the-swiftsupport-folder-is-empty)

---------

好像以前见人提过，脚本没把SwiftSupport目录拷贝到ipa里，估计是这个原因，在设置项里把 EMBEDDED_CONTENT_CONTAINS_SWIFT 设置为YES，然后用Xcode提交试试

---------

[ http://stackoverflow.com/questions/26163856/invalid-swift-support-invalid-implementation-of-swift]( http://stackoverflow.com/questions/26163856/invalid-swift-support-invalid-implementation-of-swift)

---------

[这里有人解决过]( http://www.codernotes.cn/?p=16)

---------

我的问题是我直接用老的脚本打包，导致swift的对应framework没拷贝到包里，然后出的问题，后面暂时改成直接用xcode打包就好了·

--------
