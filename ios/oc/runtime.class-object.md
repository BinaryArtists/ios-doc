## Objective-C Runtime 运行时之一：类与对象

### Runtime 是什么？

Objective-C语言是一门动态语言，它将很多静态语言在编译和链接时期做的事放到了运行时来处理。

这种特性意味着Objective-C不仅需要一个编译器，还需要一个运行时系统来执行编译的代码。

Objc Runtime其实是一个Runtime库，它基本上是用C和汇编写的，这个库使得C语言有了面向对象的能力。

接下去看，Runtime中，如何实现类和处理对象的～

### 类与对象基础数据结构

* Class
  Objective-C类是由Class类型来表示的，它实际上是一个指向objc_class结构体的指针。它的定义如下：
  ```
  typedef struct objc_class *Class;
  ```
  查看objc/runtime.h中objc_class结构体的定义如下：
  ```
  struct objc_class {
      Class isa  OBJC_ISA_AVAILABILITY;

  #if !__OBJC2__
      Class super_class                       OBJC2_UNAVAILABLE;  // 父类
      const char *name                        OBJC2_UNAVAILABLE;  // 类名
      long version                            OBJC2_UNAVAILABLE;  // 类的版本信息，默认为0
      long info                               OBJC2_UNAVAILABLE;  // 类信息，供运行期使用的一些位标识
      long instance_size                      OBJC2_UNAVAILABLE;  // 该类的实例变量大小
      struct objc_ivar_list *ivars            OBJC2_UNAVAILABLE;  // 该类的成员变量链表
      struct objc_method_list **methodLists   OBJC2_UNAVAILABLE;  // 方法定义的链表
      struct objc_cache *cache                OBJC2_UNAVAILABLE;  // 方法缓存
      struct objc_protocol_list *protocols    OBJC2_UNAVAILABLE;  // 协议链表
  #endif

  } OBJC2_UNAVAILABLE;
  ```

  在这个定义中，下面几个字段是我们感兴趣的：
    > isa：需要注意的是在Objective-C中，所有的类自身也是一个对象，这个对象的Class里面也有一个isa指针，它指向metaClass(元类)，我们会在后面介绍它。
    > super_class：指向该类的父类，如果该类已经是最顶层的根类(如NSObject或NSProxy)，则super_class为NULL。
    > cache：用于缓存最近使用的方法。一个接收者对象接收到一个消息时，它会根据isa指针去查找能够响应这个消息的对象。在实际使用中，这个对象只有一部分方法是常用的，很多方法其实很少用或者根本用不上。这种情况下，如果每次消息来时，我们都是methodLists中遍历一遍，性能势必很差。这时，cache就派上用场了。在我们每次调用过一个方法后，这个方法就会被缓存到cache列表中，下次调用的时候runtime就会优先去cache中查找，如果cache没有，才去methodLists中查找方法。这样，对于那些经常用到的方法的调用，但提高了调用的效率。
    > version：我们可以使用这个字段来提供类的版本信息。这对于对象的序列化非常有用，它可是让我们识别出不同类定义版本中实例变量布局的改变。

  * objc_object与id
    objc_object是表示一个类的实例的结构体，它的定义如下(objc/objc.h)：
    ```
    struct objc_object {
        Class isa  OBJC_ISA_AVAILABILITY;
    };
    typedef struct objc_object *id;
    ```
    可以看到，这个结构体只有一个字段，即指向其类的isa指针。这样，当我们向一个Objective-C对象发送消息时，运行时库会根据实例对象的isa指针找到这个实例对象所属的类。Runtime库会在类的方法列表及父类的方法列表中去寻找与消息对应的selector指向的方法。找到后即运行这个方法。

    当创建一个特定类的实例对象时，分配的内存包含一个objc_object数据结构，然后是类的实例变量的数据。NSObject类的alloc和allocWithZone:方法使用函数class_createInstance来创建objc_object数据结构。

    另外还有我们常见的id，它是一个objc_object结构类型的指针。它的存在可以让我们实现类似于C++中泛型的一些操作。该类型的对象可以转换为任何一种对象，有点类似于C语言中void *指针类型的作用*。

  * objc_cache
    上面提到了objc_class结构体中的cache字段，它用于缓存调用过的方法。这个字段是一个指向objc_cache结构体的指针，其定义如下：
    ```
    struct objc_cache {
        unsigned int mask /* total = mask + 1 */                 OBJC2_UNAVAILABLE;
        unsigned int occupied                                    OBJC2_UNAVAILABLE;
        Method buckets[1]                                        OBJC2_UNAVAILABLE;
    };
    ```
    该结构体的字段描述如下：
      > mask：一个整数，指定分配的缓存bucket的总数。在方法查找过程中，Objective-C runtime使用这个字段来确定开始线性查找数组的索引位置。指向方法selector的指针与该字段做一个AND位操作(index = (mask & selector))。这可以作为一个简单的hash散列算法。
      > occupied：一个整数，指定实际占用的缓存bucket的总数。
      > buckets：指向Method数据结构指针的数组。这个数组可能包含不超过mask+1个元素。需要注意的是，指针可能是NULL，表示这个缓存bucket没有被占用，另外被占用的bucket可能是不连续的。这个数组可能会随着时间而增长。

  * 元类(Meta Class)
    在上面我们提到，所有的类自身也是一个对象，我们可以向这个对象发送消息(即调用类方法)。如：
    ```
    NSArray *array = [NSArray array];
    ```
    这个例子中，+array消息发送给了NSArray类，而这个NSArray也是一个对象。既然是对象，那么它也是一个objc_object指针，它包含一个指向其类的一个isa指针。那么这些就有一个问题了，这个isa指针指向什么呢？为了调用+array方法，这个类的isa指针必须指向一个包含这些类方法的一个objc_class结构体。这就引出了meta-class的概念：meta-class是一个类对象的类。

    当我们向一个对象发送消息时，runtime会在这个对象所属的这个类的方法列表中查找方法；而向一个类发送消息时，会在这个类的meta-class的方法列表中查找。
    meta-class之所以重要，是因为它存储着一个类的所有类方法。每个类都会有一个单独的meta-class，因为每个类的类方法基本不可能完全相同。

    再深入一下，meta-class也是一个类，也可以向它发送一个消息，那么它的isa又是指向什么呢？为了不让这种结构无限延伸下去，Objective-C的设计者让所有的meta-class的isa指向基类的meta-class，以此作为它们的所属类。即，任何NSObject继承体系下的meta-class都使用NSObject的meta-class作为自己的所属类，而基类的meta-class的isa指针是指向它自己。这样就形成了一个完美的闭环。

    通过上面的描述，再加上对objc_class结构体中super_class指针的分析，我们就可以描绘出类及相应meta-class类的一个继承体系了，如下图所示：
    [!image](../images/meta-class.png)
    对于NSObject继承体系来说，其实例方法对体系中的所有实例、类和meta-class都是有效的；而类方法对于体系内的所有类和meta-class都是有效的。

### 类与对象操作函数




### 动态创建类和对象




### 实例操作函数



### Reference

  * [Objective-c的对象模型与运行时](http://www.molotang.com/articles/1912.html)
  * [Objective-C Runtime 运行时之一：类与对象](http://www.cocoachina.com/ios/20141031/10105.html)
