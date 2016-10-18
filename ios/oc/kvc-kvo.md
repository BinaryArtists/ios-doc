## KVC KVO

[如何自己动手实现 KVO](http://tech.glowing.com/cn/implement-kvo/)

参考：

### Content

KVC的消息传递
KVC容器操作
KVC与容器类(集合代理对象)
KVO和容器类
KVO的实现原理

### KVC的消息传递

valueForKey:的使用并不仅仅用来取值那么简单，还有很多特殊的用法，集合类也覆盖了这个方法，通过调用valueForKey:给容器中每一个对象发送操作消息，并且结果会被保存在一个新的容器中返回，这样我们能很方便地利用一个容器对象创建另一个容器对象。另外，valueForKeyPath:还能实现多个消息的传递。一个例子:
```
NSArray *array = [NSArray arrayWithObject:@"10.11",
@"20.22", nil];
NSArray *resultArray = [array valueForKeyPath:@"doubleValue.intValue"];
NSLog(@"%@", resultArray);
//打印结果
(
    10,
    20
)
```

### KVC容器操作

容器不仅仅能使用KVC方法实现对容器成员传递普通的操作消息，KVC还定义了特殊的一些常用操作，使用valueForKeyPath:结合操作符来使用，所定义的keyPath格式入下图所示

keyPathToCollection.@collectionOperator.keyPathToProperty
|--Left key path --|Collection operator|-Right key path-|

Left key path:如果有，则代表需要操作的对象路径(相对于调用者)

Collection operator:以"@"开头的操作符

Right key path:指定被操作的属性

常规操作符：

@avg、@count、@max、@min、@sum

对象操作符：

* @distinctUnionOfObjects、@unionOfObjects
```
NSArray *values = [object valueForKeyPath:@"@unionOfObjects.value"];
```

* @distinctUnionOfObjects操作符返回被操作对象指定属性的集合并做去重操作，而@unionOfObjects则允许重复。如果其中任何涉及的对象为nil，则抛出异常。

Array和Set操作符：

Array和Set操作符操作对象是嵌套型的集合对象

@distinctUnionOfArrays、@unionOfArrays
```
NSArray *values = [arrayOfobjectsArrays valueForKeyPath:@"@distinctUnionOfArrays.value"];
```

同样的，返回被操作集合下的集合中的对象的指定属性的集合，并且做去重操作，而@unionOfObjects则允许重复。如果其中任何涉及的对象为nil，则抛出异常。

@distinctUnionOfSets
```
NSSet *values = [setOfobjectsSets valueForKeyPath:@"@distinctUnionOfSets.value"];
```
返回结果同理于NSArray。

据官方文档说明，目前还不支持自动以操作符。

### KVC与容器类(集合代理对象)

当然对象的属性可以是一对一的，也可以是一对多。属性的一对多关系其实就是一种对容器类的映射。如果有一个名为numbers的数组属性，我们可以使用valueForKey:@"numbers"来获取，这个是没问题的，但KVC还能使用更灵活的方式管理集合。——集合代理对象
```
ElfinsArray.h
@interface ElfinsArray : NSObject
@property (assign ,nonatomic) NSUInteger count;
- (NSUInteger)countOfElfins;
- (id)objectInElfinsAtIndex:(NSUInteger)index;
@end
ElfinsArray.m
#import "ElfinsArray.h"
@implementation ElfinsArray
- (NSUInteger)countOfElfins {
    return  self.count;
}
- (id)objectInElfinsAtIndex:(NSUInteger)index {
    return [NSString stringWithFormat:@"小精灵%lu", (unsigned long)index];
}
@end
Main.m
- (void)work {
    ElfinsArray *elfinsArr = [ElfinsArray alloc] init];
    elfinsArr.count = 3;
    NSArray *elfins = [ElfinsArray valueForKey:@"elfins"];
    //elfins为KVC代理数组
    NSLog(@"%@", elfins);
    //打印结果
    (
        "小精灵0",
        "小精灵1",
        "小精灵2"
    )
}
```
问题来了，ElfinsArray中并没有定义elfins属性，那么elfins数组从何而来？valueForKey:有如下的搜索规则：

按顺序搜索getVal、val、isVal，第一个被找到的会用作返回。
countOfVal，或者objectInValAtIndex:与valAtIndexes其中之一，这个组合会使KVC返回一个代理数组。
countOfVal、enumeratorOfVal、memberOfVal。这个组合会使KVC返回一个代理集合。
名为val、isVal、val、isVal的实例变量。到这一步时，KVC会直接访问实例变量，而这种访问操作破坏了封装性，我们应该尽量避免，这可以通过重写+accessInstanceVariablesDirectly返回NO来避免这种行为。
ok上例中我们实现了第二条中的特殊命名函数组合：
```
- (NSUInteger)countOfElfins;
- (id)objectInElfinsAtIndex:(NSUInteger)index;
```
这使得我们调用valueForKey:@"elfins"时，KVC会为我们返回一个可以响应NSArray所有方法的代理数组对象(NSKeyValueArray)，这是NSArray的子类，- (NSUInteger)countOfElfins决定了这个代理数组的容量，- (id)objectInElfinsAtIndex:(NSUInteger)index决定了代理数组的内容。本例中使用的key是elfins，同理的如果key叫human，KVC就会去寻找-countOfHuman:

### 可变容器呢

当然我们也可以在可变集合(NSMutableArray、NSMutableSet、NSMutableOrderedSet)中使用集合代理:

这个例子我们不再使用KVC给我们生成代理数组，因为我们是通过KVC拿到的，而不能主动去操作它(insert/remove)，我们声明一个可变数组属性elfins。
```
ElfinsArray.h
@interface ElfinsArray : NSObject
@property (strong ,nonatomic) NSMutableArray *elfins;
- (void)insertObject:(id)object inNumbersAtIndex:(NSUInteger)index;
- (void)removeObjectFromNumbersAtIndex:(NSUInteger)index;
@end
ElfinsArray.m
#import "ElfinsArray.h"
@implementation ElfinsArray
- (void)insertObject:(id)object inElfinsAtIndex:(NSUInteger)index {
[self.elfins insertObject:object atIndex:index];
NSLog(@"insert %@\n", object);
}
- (void)removeObjectFromElfinsAtIndex:(NSUInteger)index {
    [self.elfins removeObjectAtIndex:index];
    NSLog(@"remove\n");
}
@end
Main.m
- (void)work {
    ElfinsArray *elfinsArr = [ElfinsArray alloc] init];
    elfinsArr.elfins = [NSMutableArray array];
    NSMutableArray *delegateElfins = [ElfinsArray mutableArrayValueForKey:@"elfins"];
    //delegateElfins为KVC代理可变数组，非指向elfinsArr.elfins
    [delegateElfins insertObject:@"小精灵10" atIndex:0];
    NSLog(@"first log \n %@", elfinsArr.elfins);
    [delegateElfins removeObjectAtIndex:0];
    NSLog(@"second log \n %@", elfinsArr.elfins);
    //打印结果
    insert 小精灵10
    first log
    (
        "小精灵10"
    )
    remove
    second log
    (
    )
}
```
上例中，我们通过调用
```
- mutableArrayValueForKey:
- mutableSetValueForKey:
- mutableOrderedSetValueForKey:
```
KVC会给我们返回一个代理可变容器delegateElfins，通过对代理可变容器的操作，KVC会自动调用合适KVC方法(如下):

//至少实现一个insert方法和一个remove方法
```
- insertObject:inValAtIndex:
- removeObjectFromValAtIndex:
- insertVal:atIndexes:
- removeValAtIndexes:
```
间接地对被代理对象操作。

还有一组更强大的方法供参考
```
- replaceObjectInValAtIndex:withObject:
- replaceValAtIndexes:withVal:
```
我认为这就是KVC结合KVO的结果。这里我尝试研究下了文档中的如下两个方法，还没有什么头绪，知道的朋友可否告诉我下
```
- willChange:valuesAtIndexes:forKey:
- didChange:valuesAtIndexes:forKey:
```

### KVO和容器类

要注意，对容器类的观察与对非容器类的观察并不一样，不可变容器的内容发生改变并不会影响他们所在的容器，可变容器的内容改变&内容增删也都不会影响所在的容器，那么如果我们需要观察某容器中的对象，首先我们得观察容器内容的变化，在容器内容增加时添加对新内容的观察，在内容移除同时移除对该内容的观察。

既然容器内容数量改变和内容自身改变都不会触发容器改变，此时对容器属性施加KVO并没有效果，那么怎么实现对容器变化(非容器改变)的观察呢？上面所介绍的代理容器能帮到我们：
```
//我们通过KVC拿到容器属性的代理对象
NSMutableArray *delegateElfins = [ElfinsArray mutableArrayValueForKey:@"elfins"];
[delegateElfins addObject:@"小精灵10"];
```
当然这样做的前提是要实现insertObject:inValAtIndex:和removeObjectFromValAtIndex:两个方法。如此才能触发observeValueForKeyPath:ofObject:change:context:的响应。

而后，我们就可以轻而易举地在那两个方法实现内对容器新成员添加观察/对容器废弃成员移除观察。

### KVO的实现原理

？？？？未完。。。

### 参考

  * [KVC KVO高阶应用](http://www.cocoachina.com/ios/20160427/16028.html)
  * [bjc.io](https://www.objc.io/issues/7-foundation/key-value-coding-and-observing/)
  * [NSKeyValueObserving Protocol Reference](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Protocols/NSKeyValueObserving_Protocol/#//apple_ref/occ/instm/NSObject/willChange:valuesAtIndexes:forKey:)
