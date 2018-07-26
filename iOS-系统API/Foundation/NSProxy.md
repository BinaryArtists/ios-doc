# NSProxy

遵守了 NSObject 协议，并且第一个 Ivar 是一个 isa 指针，因此它完全是可以拿来当一个 NSObject 或其派生类来使用的

## 协议

```
- (void)forwardInvocation:(NSInvocation *)invocation;
- (nullable NSMethodSignature *)methodSignatureForSelector:(SEL)sel;
```

## 例子

```
@interface MyProxy : NSProxy {
    id _object; // 被代理对象的引用
}

+ (id)proxyForObject:(id)obj;

@end


@implementation MyProxy

+ (id)proxyForObject:(id)obj {
    MyProxy *instance = [MyProxy alloc];
    instance->_object = obj;
    
    return instance;
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel { // methodSignatureForSelector: 方法需要获取一个方法签名，用来生成 NSInvocation，我们直接将这个调用转发到被代理对象中
    return [_object methodSignatureForSelector:sel];
}

- (void)forwardInvocation:(NSInvocation *)invocation {
    if ([_object respondsToSelector:invocation.selector]) {
        NSString *selectorName = NSStringFromSelector(invocation.selector);
        
        NSLog(@"Before calling \"%@\".", selectorName);
        [invocation invokeWithTarget:_object];
        NSLog(@"After calling \"%@\".", selectorName);
    }
}

@end


// 测试
int main(int argc, char *argv[]) {
    dispatch_semaphore_t sem = dispatch_semaphore_create(0);
-
    NSURL *url = [MyProxy proxyForObject:[NSURL URLWithString:@"https://www.google.com"]];
    NSURLSessionDataTask *task = [[NSURLSession sharedSession] dataTaskWithURL:url completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
        dispatch_semaphore_signal(sem);
    }];
-
    [task resume];
    dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);
-
    return 0;
}

```


