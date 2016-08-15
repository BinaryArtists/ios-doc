## Objective-c 的 属性修饰符

参考：[ IOS atomic与nonatomic，assign,copy与retain的定义和区别](http://blog.csdn.net/mars2639/article/details/7352540)

## 正文

  1. atomic和nonatomic用来决定编译器生成的getter和setter是否为原子操作。
    * atomic
                设置成员变量的@property属性时，默认为atomic，提供多线程安全。
                在多线程环境下，原子操作是必要的，否则有可能引起错误的结果。加了atomic，setter函数会变成下面这样：
                        {lock}
                                if (property != newValue) {
                                        [property release];
                                        property = [newValue retain];
                                }
                        {unlock}
    * nonatomic
        禁止多线程，变量保护，提高性能。
        atomic是Objc使用的一种线程保护技术，基本上来讲，是防止在写未完成的时候被另外一个线程读取，造成数据错误。而这种机制是耗费系统资源的，所以在iPhone这种小型设备上，如果没有使用多线程间的通讯编程，那么nonatomic是一个非常好的选择。
        指出访问器不是原子操作，而默认地，访问器是原子操作。这也就是说，在多线程环境下，解析的访问器提供一个对属性的安全访问，从获取器得到的返回值或者通过设置器设置的值可以一次完成，即便是别的线程也正在对其进行访问。如果你不指定 nonatomic ，在自己管理内存的环境中，解析的访问器保留并自动释放返回的值，如果指定了 nonatomic ，那么访问器只是简单地返回这个值。

  2. assign
        对基础数据类型 （NSInteger，CGFloat）和C数据类型（int, float, double, char）等等。
        此标记说明设置器直接进行赋值，这也是默认值。在使用垃圾收集的应用程序中，如果你要一个属性使用assign，且这个类符合NSCopying协             议，你就要明确指出这个标记，而不是简单地使用默认值，否则的话，你将得到一个编译警告。这再次向编译器说明你确实需要赋值，即使它是           可拷贝的。

3. retain
        对其他NSObject和其子类对参数进行release旧值，再retain新值
        指定retain会在赋值时唤醒传入值的retain消息。此属性只能用于Objective-C对象类型，而不能用于Core Foundation对象。(原因很明显，retain会增加对象的引用计数，而基本数据类型或者Core Foundation对象都没有引用计数——译者注)。
        注意: 把对象添加到数组中时，引用计数将增加对象的引用次数+1。

4. copy
        对NSString 它指出，在赋值时使用传入值的一份拷贝。拷贝工作由copy方法执行，此属性只对那些实行了NSCopying协议的对象类型有效。更深入的讨论，请参考“复制”部分。

5. copy与retain：
Copy其实是建立了一个相同的对象，而retain不是：
1.比如一个NSString 对象，地址为0×1111 ，内容为@”STR”，Copy 到另外一个NSString 之后，地址为0×2222 ，内容相同。
2.新的对象retain为1 ，旧有对象没有变化retain 到另外一个NSString 之后，地址相同（建立一个指针，指针拷贝），内容当然相同，这个对象的retain值+1。
总结：retain 是指针拷贝，copy 是内容拷贝。

6. assign与retain：
  * 接触过C，那么假设你用malloc分配了一块内存，并且把它的地址赋值给了指针a，后来你希望指针b也共享这块内存，于是你又把a赋值给（assign）了b。此时a和b指向同一块内存，请问当a不再需要这块内存，能否直接释放它？答案是否定的，因为a并不知道b是否还在使用这块内存，如果a释放了，那么b在使用这块内存的时候会引起程序crash掉。
  * 了解到1中assign的问题，那么如何解决？最简单的一个方法就是使用引用计数（reference counting），还是上面的那个例子，我们给那块内存设一个引用计数，当内存被分配并且赋值给a时，引用计数是1。当把a赋值给b时引用计数增加到2。这时如果a不再使用这块内存，它只需要把引用计数减1，表明自己不再拥有这块内存。b不再使用这块内存时也把引用计数减1。当引用计数变为0的时候，代表该内存不再被任何指针所引用，系统可以把它直接释放掉。
总结：上面两点其实就是assign和retain的区别，assign就是直接赋值，从而可能引起1中的问题，当数据为int, float等原生类型时，可以使用assign。retain就如2中所述，使用了引用计数，retain引起引用计数加1, release引起引用计数减1，当引用计数为0时，dealloc函数被调用，内存被回收。
