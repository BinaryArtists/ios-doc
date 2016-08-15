## [Objective-C KVC 自动转换类型研究](http://www.2cto.com/kf/201410/346170.html)

apple很厚道,kvc的时候帮我们做了一些类型转换,规律贴出来,给大伙参考参考

```objc
@interface Entity : NSObject
 
@property (nonatomic, copy) NSString *str;
 
@property (nonatomic, assign) int i1;
@property (nonatomic, assign) int i2;
@property (nonatomic, assign) int i3;
 
@property (nonatomic, assign) float f1;
 
@property (nonatomic, strong) NSNumber *num;
 
@property (nonatomic, assign) BOOL b1;
@property (nonatomic, assign) BOOL b2;
@property (nonatomic, assign) BOOL b3;
 
@property (nonatomic, strong) NSDate *date1;
 
@property (nonatomic, assign) NSTimeInterval t1;
 
@end
 
    // 数值的字串可以转成数值类型
    [obj setValue:@"2.4" forKey:@"i1"];
    // 非数值的字串不认识
    [obj setValue:@"a" forKey:@"i2"];
    // 只认识特殊符号以前的数
    [obj setValue:@"2014 10-24" forKey:@"i3"];
     
    // 这个转换过去是NSNumber类型
    [obj setValue:@1 forKey:@"str"];
    [obj setValue:@1.23 forKey:@"f1"];
     
    // 转去还是NSString
    [obj setValue:@"99" forKey:@"num"];
     
    // >= 1.0 为真, 1.0以下为假
    [obj setValue:@0.9 forKey:@"b1"];
    // >= 1.0 为真, 1.0以下为假
    [obj setValue:@"1.1" forKey:@"b2"];
    // true TRUE yes YES false FALSE no NO 都可以识别
    [obj setValue:@"TRUE" forKey:@"b3"];
     
    // 转去还是NSTring
    [obj setValue:@"2014-10-24" forKey:@"date1"];
    // 不认识
    [obj setValue:@"2014-10-24" forKey:@"t1"];
     
    // 结论
    // 数值类型的字串 to 数值 可以转行成数值
    // 类 to 类 不转换直接赋值
```
