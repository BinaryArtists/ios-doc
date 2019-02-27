# NSCache

常用的SDWebImage图片下载库的缓存机制就是通过NSCache来实现的。《Effective Objective-C 2.0》中也专门用一小篇的内容来介绍NSCache的使用(第50条：构建缓存时选用NSCache而非NSDictionary)，里面有更精彩的内容。如果我们需要构建缓存机制，则应该使用NSCache，而不是NSDictionary，这样可以减少我们应用对内存的占用，从而达到优化内存的目标。

## 说明

NSCache是一个类似于集合的容器，即缓存。它存储key-value对，这一点类似于NSDictionary类。

### NSDiscardableContent协议

存储在NSCache中的通用数据类型通常是实现了NSDiscardableContent协议的对象。

```
@protocol NSDiscardableContent
@required
- (BOOL)beginContentAccess;
- (void)endContentAccess;
- (void)discardContentIfPossible;
- (BOOL)isContentDiscarded;
@end
```

默认情况下，缓存中的NSDiscardableContent对象在其内容被丢弃时，会被移除出缓存，尽管我们可以改变这种缓存策略。如果一个NSDiscardableContent被放进缓存，则在对象被移除时，缓存会调用discardContentIfPossible方法。

* ```- (BOOL)beginContentAccess, - (void)endContentAccess```: 默认情况下，NSDiscardableContent对象的counter变量初始值为1，以确保对象不会被内存管理系统立即释放。从这个点开始，我们就需要去跟踪counter变量的状态。为此。协议声明了两个方法：beginContentAccess和endContentAccess。
    - ```- (BOOL)beginContentAccess```调用beginContentAccess方法会增加对象的counter变量(+1)，这样就可以确保对象不会被丢弃。通常我们在对象被需要或者将要使用时调用这个方法。具体的实现类可以决定在对象已经被丢弃的情况下是否重新创建这些内存，且重新创建成功后返回YES。协议的实现者在NSDiscardableContent对象被使用，而又没有调用它的beginContentAccess方法时，应该抛出一个异常。
        > 函数的返回值如果是YES，则表明可丢弃内存仍然可用且已被成功访问；否则返回NO。另外需要注意的是，该方法是在实现类中必须实现(required)。
    - ```- (void)endContentAccess``` 与beginContentAccess相对应的是endContentAccess。如果可丢弃内存不再被访问时调用。该方法会减少对象的counter变量，通常是让对象的counter值变回为0，这样在对象的内容不再被需要时，就要以将其丢弃。

### NSCacheDelegate代理

实现NSCacheDelegate代理的对象会在对象即将从缓存中移除时执行一些特定的操作，因此代理对象可以实现以下方法

```
- (void)cache:(NSCache *)cache willEvictObject:(id)obj
```

需要注意的是在这个代理方法中不能修改cache对象。

### NSCache与可变集合有几点不同

* NSCache类结合了各种自动删除策略，以确保不会占用过多的系统内存。如果其它应用需要内存时，系统自动执行这些策略。当调用这些策略时，会从缓存中删除一些对象，以最大限度减少内存的占用。
* NSCache是线程安全的!我们可以在不同的线程中添加、删除和查询缓存中的对象，而不需要锁定缓存区域。
* 不像NSMutableDictionary对象，一个缓存对象不会拷贝key对象。

### 缓存属性配置

* countLimit：限定了缓存最多维护的对象的个数
    - 默认值为0，表示不限制数量。但需要注意的是，这不是一个严格的限制。如果缓存的数量超过这个数量，缓存中的一个对象可能会被立即丢弃、或者稍后、也可能永远不会，具体依赖于缓存的实现细节
* totalCostLimit：限定了缓存能维持的最大内存
    - 默认值也是0，表示没有限制。当我们添加一个对象到缓存中时，我们可以为其指定一个消耗(cost)，如对象的字节大小。如果添加这个对象到缓存导致缓存总的消耗超过totalCostLimit的值，则缓存会自动丢弃一些对象，直到总消耗低于totalCostLimit值。不过被丢弃的对象的顺序无法保证。
* ```- (void)setObject:(id)obj forKey:(id)key cost:(NSUInteger)num``` ：在存储对象时，可以为对象指定一个消耗值
    - 这个消耗值用于计算缓存中所有对象的一个消耗总和。当内存受限或者总消耗超过了限定的最大总消耗，则缓存应该开启一个丢弃过程以移除一些对象。不过，这个过程不能保证被丢弃对象的顺序。其结果是，如果我们试图操作这个消耗值来实现一些特殊的行为，则后果可能会损害我们的程序。通常情况下，这个消耗值是对象的字节大小。如果这些信息不是现成的，则我们不应该去计算它，因为这样会使增加使用缓存的成本。如果我们没有可用的值传递，则直接传递0，或者是使用-setObject:forKey:方法，这个方法不需要传入一个消耗值.
* evictsObjectsWithDiscardedContent：标识缓存是否自动舍弃那些内存已经被丢弃的对象(discardable-content object)

## 使用

```
// 定义缓存池
_cache = [[NSCache alloc] init];
_cache.countLimit = 5;
_cache.totalCostLimit = 1024 * 5;

// 设置、实现代理
self.cache.delegate = self

//当缓存被移除的时候执行
- (void)cache:(NSCache *)cache willEvictObject:(id)obj{
    NSLog(@"缓存移除  %@",obj);
}

// 写入、输出
for (int i = 0; i < 10; i++) {
    [self.cache setObject:[NSString stringWithFormat:@"hello %d",i] forKey:[NSString stringWithFormat:@"h%d",i]];
    NSLog(@"添加 %@",[NSString stringWithFormat:@"hello %d",i]);
}

// 输出缓存中的数据
for (int i = 0; i < 10; i++) {
    NSLog(@"%@",[self.cache objectForKey:[NSString stringWithFormat:@"h%d",i]]);
}

// 收到内存警告
- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];

    // 当收到内存警告，调用removeAllObjects 之后，无法再次往缓存中添加数据 (有兴趣的朋友，可以测试一下)
    [self.cache removeAllObjects];

    //输出缓存中的数据
    for (int i = 0; i < 10; i++) {
        NSLog(@"%@",[self.cache objectForKey:[NSString stringWithFormat:@"h%d",i]]);
    }
}

```

## 参考

1. [DXSmile/NSCache-some-understanding.](https://github.com/DXSmile/NSCache-some-understanding.)