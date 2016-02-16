## [objective-c访问控制符](http://www.2cto.com/kf/201410/345823.html)

objective-c中成员变量的四个访问控制符：

@private：只有当前类的内部才能访问
@public：所有人都可访问
@protected：只限当前类和它的子类能够访问
@package：让那些受它控制的成员变量不仅可以在当前类中访问，也可在相同映像的其他程序中访问。

什么是“同一映像”？

就是编译后生成的同一框架或同一个执行文件。
即当前框架能够访问，但是外部程序无法访问。（如果是public的话，外部程序也能够访问）。


@private

@package

@protected

@public

同一类型

???

???

???

???

同一映像中


???


???

子类中



???

???

全局范围内




???


例子：

接口部分：(FKApple.h)

#import

@interface FKApple:NSObject

{

//使用@package限制成员变量
@package
double _weight;
}

@end

main部分：

#import "FKApple.h"

int main(int argc, char *argv[])

{

@autoreleasepool{

FKApple *apple = [[FKApple alloc] init];
apple->_weight = 30.4;
}
}
