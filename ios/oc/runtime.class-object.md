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

runtime提供了大量的函数来操作类与对象。类的操作方法大部分是以class为前缀的，而对象的操作方法大部分是以objc或object_为前缀。下面我们将根据这些方法的用途来分类讨论这些方法的使用。

  * 类名(name)
    类名操作的函数主要有：
    ```
    // 获取类的类名
    const char * class_getName ( Class cls );
    ```
    对于class_getName函数，如果传入的cls为Nil，则返回一个字字符串。

  * 父类(super_class)和元类(meta-class)
    父类和元类操作的函数主要有：
    ```
    // 获取类的父类
    Class class_getSuperclass ( Class cls );

    // 判断给定的Class是否是一个元类
    BOOL class_isMetaClass ( Class cls );
    ```
    class_getSuperclass函数，当cls为Nil或者cls为根类时，返回Nil。不过通常我们可以使用NSObject类的superclass方法来达到同样的目的。
    class_isMetaClass函数，如果是cls是元类，则返回YES；如果否或者传入的cls为Nil，则返回NO。

  * 实例变量大小(instance_size)
  实例变量大小操作的函数有：
  ```
  // 获取实例大小
  size_t class_getInstanceSize ( Class cls );
  ```

  * 成员变量(ivars)及属性
    在objc_class中，所有的成员变量、属性的信息是放在链表ivars中的。ivars是一个数组，数组中每个元素是指向Ivar(变量信息)的指针。runtime提供了丰富的函数来操作这一字段。大体上可以分为以下几类：

    1.成员变量操作函数，主要包含以下函数：
    ```
    // 获取类中指定名称实例成员变量的信息
    Ivar class_getInstanceVariable ( Class cls, const char *name );

    // 获取类成员变量的信息
    Ivar class_getClassVariable ( Class cls, const char *name );

    // 添加成员变量
    BOOL class_addIvar ( Class cls, const char *name, size_t size, uint8_t alignment, const char *types );

    // 获取整个成员变量列表
    Ivar * class_copyIvarList ( Class cls, unsigned int *outCount );
    ```
    class_getInstanceVariable函数，它返回一个指向包含name指定的成员变量信息的objc_ivar结构体的指针(Ivar)。
    class_getClassVariable函数，目前没有找到关于Objective-C中类变量的信息，一般认为Objective-C不支持类变量。注意，返回的列表不包含父类的成员变量和属性。

    Objective-C不支持往已存在的类中添加实例变量，因此不管是系统库提供的提供的类，还是我们自定义的类，都无法动态添加成员变量。但如果我们通过运行时来创建一个类的话，又应该如何给它添加成员变量呢？这时我们就可以使用class_addIvar函数了。不过需要注意的是，这个方法只能在objc_allocateClassPair函数与objc_registerClassPair之间调用。另外，这个类也不能是元类。成员变量的按字节最小对齐量是1<

    class_copyIvarList函数，它返回一个指向成员变量信息的数组，数组中每个元素是指向该成员变量信息的objc_ivar结构体的指针。这个数组不包含在父类中声明的变量。outCount指针返回数组的大小。需要注意的是，我们必须使用free()来释放这个数组。

    2.属性操作函数，主要包含以下函数：
    ```
    // 获取指定的属性
    objc_property_t class_getProperty ( Class cls, const char *name );

    // 获取属性列表
    objc_property_t * class_copyPropertyList ( Class cls, unsigned int *outCount );

    // 为类添加属性
    BOOL class_addProperty ( Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount );

    // 替换类的属性
    void class_replaceProperty ( Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount );
    ```
    这一种方法也是针对ivars来操作，不过只操作那些是属性的值。我们在后面介绍属性时会再遇到这些函数。

    3.在MAC OS X系统中，我们可以使用垃圾回收器。runtime提供了几个函数来确定一个对象的内存区域是否可以被垃圾回收器扫描，以处理strong/weak引用。这几个函数定义如下：
    ```
    const uint8_t * class_getIvarLayout ( Class cls );
    void class_setIvarLayout ( Class cls, const uint8_t *layout );
    const uint8_t * class_getWeakIvarLayout ( Class cls );
    void class_setWeakIvarLayout ( Class cls, const uint8_t *layout );
    ```

    但通常情况下，我们不需要去主动调用这些方法；在调用objc_registerClassPair时，会生成合理的布局。在此不详细介绍这些函数。

  * 方法(methodLists)
    方法操作主要有以下函数：
    ```
    // 添加方法
    BOOL class_addMethod ( Class cls, SEL name, IMP imp, const char *types );

    // 获取实例方法
    Method class_getInstanceMethod ( Class cls, SEL name );

    // 获取类方法
    Method class_getClassMethod ( Class cls, SEL name );

    // 获取所有方法的数组
    Method * class_copyMethodList ( Class cls, unsigned int *outCount );

    // 替代方法的实现
    IMP class_replaceMethod ( Class cls, SEL name, IMP imp, const char *types );

    // 返回方法的具体实现
    IMP class_getMethodImplementation ( Class cls, SEL name );
    IMP class_getMethodImplementation_stret ( Class cls, SEL name );

    // 类实例是否响应指定的selector
    BOOL class_respondsToSelector ( Class cls, SEL sel );
    ```
    class_addMethod的实现会覆盖父类的方法实现，但不会取代本类中已存在的实现，如果本类中包含一个同名的实现，则函数会返回NO。如果要修改已存在实现，可以使用method_setImplementation。一个Objective-C方法是一个简单的C函数，它至少包含两个参数—self和_cmd。所以，我们的实现函数(IMP参数指向的函数)至少需要两个参数，如下所示：
    ```
    void myMethodIMP(id self, SEL _cmd)
    {
        // implementation ....
    }
    ```
    与成员变量不同的是，我们可以为类动态添加方法，不管这个类是否已存在。

    另外，参数types是一个描述传递给方法的参数类型的字符数组，这就涉及到类型编码，我们将在后面介绍。

    class_getInstanceMethod、class_getClassMethod函数，与class_copyMethodList不同的是，这两个函数都会去搜索父类的实现。

    class_copyMethodList函数，返回包含所有实例方法的数组，如果需要获取类方法，则可以使用class_copyMethodList(object_getClass(cls), &count)(一个类的实例方法是定义在元类里面)。该列表不包含父类实现的方法。outCount参数返回方法的个数。在获取到列表后，我们需要使用free()方法来释放它。

    class_replaceMethod函数，该函数的行为可以分为两种：如果类中不存在name指定的方法，则类似于class_addMethod函数一样会添加方法；如果类中已存在name指定的方法，则类似于method_setImplementation一样替代原方法的实现。

    class_getMethodImplementation函数，该函数在向类实例发送消息时会被调用，并返回一个指向方法实现函数的指针。这个函数会比method_getImplementation(class_getInstanceMethod(cls, name))更快。返回的函数指针可能是一个指向runtime内部的函数，而不一定是方法的实际实现。例如，如果类实例无法响应selector，则返回的函数指针将是运行时消息转发机制的一部分。

    class_respondsToSelector函数，我们通常使用NSObject类的respondsToSelector:或instancesRespondToSelector:方法来达到相同目的。

  * 协议(objc_protocol_list)
    协议相关的操作包含以下函数：
    ```
    // 添加协议
    BOOL class_addProtocol ( Class cls, Protocol *protocol );

    // 返回类是否实现指定的协议
    BOOL class_conformsToProtocol ( Class cls, Protocol *protocol );

    // 返回类实现的协议列表
    Protocol * class_copyProtocolList ( Class cls, unsigned int *outCount );
    ```
    class_conformsToProtocol函数可以使用NSObject类的conformsToProtocol:方法来替代。

    class_copyProtocolList函数返回的是一个数组，在使用后我们需要使用free()手动释放。

  * 版本(version)
    版本相关的操作包含以下函数：
    ```
    // 获取版本号
    int class_getVersion ( Class cls );

    // 设置版本号
    void class_setVersion ( Class cls, int version );
    其它
    ```
    runtime还提供了两个函数来供CoreFoundation的tool-free bridging使用，即：
    ```
    Class objc_getFutureClass ( const char *name );
    void objc_setFutureClass ( Class cls, const char *name );
    ```
    通常我们不直接使用这两个函数。

### 动态创建类和对象

runtime的强大之处在于它能在运行时创建类和对象。



### 实例操作函数



### Reference

  * [Objective-c的对象模型与运行时](http://www.molotang.com/articles/1912.html)
  * [Objective-C Runtime 运行时之一：类与对象](http://www.cocoachina.com/ios/20141031/10105.html)
