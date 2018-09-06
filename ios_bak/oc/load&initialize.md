# load & initialize

### 

[NSObject的load和initialize方法](http://www.cocoachina.com/ios/20150104/10826.html)

### 为什么我们说，在load中无需，甚至必须不去调用 [super load]

我遇到一个案例是这样的，它发生在class、category（或者class的子类）加载的阶段：（我们主要关注，同一个类根的一组类）

1. 我们都知道class的加载，早于category的加载；
2. 有时候我们的需求，会在load过程中，对元类（meta class）进行实现（implementation）修改；
3. 比如会做method swizzle，这里要注意，“时序”一定不能错！
4. 举例：在meta class加载完毕，正在加载category的时候，我们进行了method swizzle，然后在加载相同meta class的category的时候～
5. 此时我们去调用 [super load] ，那meta 会重新加载，实现（implementation）会refresh，那么先前的 method swizzle 操作则无效。
6. 结论：
    i. 不要去调用 [super load]
    ii. 或者，重新选择 method swizzle 的时机
    iii. 谨慎使用 method swizzle