## 协议（未完成）

### 正式协议

### 非正式协议 (informal protocol)

*以下引用自[Objective-C如何自己实现一个基于数组下标的属性访问模式](http://blog.csdn.net/likendsl/article/details/44671823)*

在iOS6.0以及OS X10.8之后，Apple引入了一套非正式协议（informal protocol）与Objective-C语法直接绑定。当你实现了这其中的方法之后即可使用数组下标来访问属性元素。

在Foundation库中，NSArray类实现了- (id)objectAtIndexedSubscript:(NSUInteger)idx方法。因此，我们可以这么来访问数组元素：
```
NSArray *arr = @[@100, @200, @300];
NSNumber *num = arr[0];
上述arr[0]就相当于[arr objectAtIndex:0]。
```

而NSMutableArray在基于NSArray的基础上又实现了- (void)setObject:(id)anObject atIndexedSubscript:(NSUInteger)index方法。这样我们可以通过数组下标来读写相应元素，比如：
```
NSMutableArray *arr = [NSMutableArray arrayWithArray:@[@100, @200, @300]];
arr[2] = arr[0];
```

而NSDictionary类实现了- (id)objectForKeyedSubscript:(id)key方法。这样我们能以数组下标的形式来访问相应键的值。比如：
```
NSDictionary *dict = @{@"key" : @"value"};
NSString *value = dict[@"key"];
```

而NSMutableDictionary在NSDictionary类的基础上又实现了- (void)setObject:(id)object forKeyedSubscript:(id < NSCopying >)aKey方法。这样，我们能以数组下标的方式来读写相应键的值。比如：
```
NSMutableDictionary *dict = [NSMutableDictionary dictionaryWithDictionary:@{@"key":"@Hello"}];
dict[dict[@"key"]] = @"world";
```

总的来看：
```
- (void)setObject:(id)object forKeyedSubscript:(id < NSCopying >)aKey;
- (id)objectForKeyedSubscript:(id)key;
- (void)setObject:(id)anObject atIndexedSubscript:(NSUInteger)index;
- (id)objectAtIndexedSubscript:(NSUInteger)idx;
```
