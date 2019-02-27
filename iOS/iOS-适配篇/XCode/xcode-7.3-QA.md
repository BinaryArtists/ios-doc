## Xcode 升级 7.3 遇到的问题

1. [Cannot create __weak reference in file using manual reference counting  ](http://blog.163.com/jesse_blog/blog/static/2373750142016224111128429/)、[ iOS9.3 __weak reference](http://blog.csdn.net/siwen1990/article/details/50971600)

如果，当你升级到xcode 7.3 后 发现遇到如下问题：

    cannot create __weak reference in file using manual reference counting

      __cpt_weak UIPinchGestureRecognizer *pinchGestureRecognizer;


那么，可以设置build setting -> Apple LLVM7.1 - Language - Objective C -> Weak References in Manual Retain Release YES

或者用__unsafe_unretained 代替__weak,但是如果用__unsafe_unretained 请注意下面的问题。

__unsafe_unretained specifies a reference that does not keep the referenced object alive and is not set to nil when there are no strong references to the object. If the object it references is deallocated, the pointer is left dangling.

》》》》》》》》》》》》》》
我这边这样修改，暂时没用，遂：直接删除__weak。

2.
