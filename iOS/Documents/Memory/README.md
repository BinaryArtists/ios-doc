
问：
偶像 问你个问题啊 一定要回答我啊 __unsafe_unretained _YYLinkedMapNode *_prev; // retained by dic
这个地方 我把前面的 __unsafe_unretained 换成__weak 是不是又可以的，大家又看到我这个评论的也回复一下 讨论一下呗

答：
可以是可以，但是可以参考作者另一篇文章：https://blog.ibireme.com/2015/10/23/ios_model_framework_benchmark/ 。文末 YYModel 性能优化的几个 Tip 中第5条
“5. 避免多余的内存管理方法
在 ARC 条件下，默认声明的对象是 __strong 类型的，赋值时有可能会产生 retain/release 调用，如果一个变量在其生命周期内不会被释放，则使用 __unsafe_unretained 会节省很大的开销。

访问具有 __weak 属性的变量时，实际上会调用 objc_loadWeak() 和 objc_storeWeak() 来完成，这也会带来很大的开销，所以要避免使用 __weak 属性。

创建和使用对象时，要尽量避免对象进入 autoreleasepool，以避免额外的资源开销。