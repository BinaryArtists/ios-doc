## 类别

参考：
1. [结合 category 工作原理分析 OC2.0 中的 runtime](http://www.cnblogs.com/fengmin/p/5739921.html)

### 基本实现与使用

略

### 给分类添加属性

原理：给一个类声明属性，其实本质就是给这个类添加关联，并不是直接把这个值的内存空间添加到类存空间。

```
@implementation ViewController
-(void)viewDidLoad {
[super viewDidLoad];
// Do any additional setup after loading the view, typically from a nib.
// 给系统NSObject类动态添加属性name
NSObject *objc = [[NSObject alloc] init];
objc.name = @"小码哥";
NSLog(@"%@",objc.name);
}
@end

// 定义关联的key
static const char *key = "name";
@implementation NSObject (Property)
(NSString *)name
{
// 根据关联的key，获取关联的值。
return objc_getAssociatedObject(self, key);
}
(void)setName:(NSString *)name
{
// 第一个参数：给哪个对象添加关联
// 第二个参数：关联的key，通过这个key获取
// 第三个参数：关联的value
// 第四个参数:关联的策略
objc_setAssociatedObject(self,key,name,OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}
@end
```

### Category原理之［runtime 初始化］

从 runtime.h 文件中一段代码开始：
```
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
```
其中 OBJC2_UNAVAILABLE 意味着，该标记的内容，都已经在 2006 年就永远的告别了我们，仅用于参考。

首先 runtime 依赖于 dyld 动态加载，在 objc-os.mm 文件中可以找到入口，它的调用栈简单整理如下:
```
void _objc_init(void)
└──const char *map_2_images(...)
    └──const char *map_images_nolock(...)
        └──void _read_images(header_info **hList, uint32_t hCount)
```

以上四个方法可以理解为 runtime 的初始化过程，我们暂且不深究。在 _read_images_ 方法中有如下代码:
```
if (cat->classMethods  ||  cat->protocols  
    /* ||  cat->classProperties */) {
    addUnattachedCategoryForClass(cat, cls->ISA(), hi);
    if (cls->ISA()->isRealized()) {
        remethodizeClass(cls->ISA());
    }
}
```

根据注释可见苹果曾经计划利用 category 来添加属性。在 addUnattachedCategoryForClass 方法中会找到当前类的所有 category，然后在 remethodizeClass 真正的去做处理。不过到目前为止还没有接触到相关的 category 处理，我们继续沿着调用栈向下走:
```
void _read_images(header_info **hList, uint32_t hCount)
└──static void remethodizeClass(Class cls)
    └──static void attachCategories(Class cls, category_list *cats, bool flush_caches)
```
这里的 attachCategories 就是处理 category 的核心所在，不过在阅读这段代码之前，我们有必要先熟悉一下相关的数据结构。

### Category原理之［相关的数据结构］

首先来了解一下一个 Category 是如何存储的，在 objc-runtime-new.h 中可以看到如下定义，我只列出了其中成员变量:
```
struct category_t {
    const char *name;
    classref_t cls;
    struct method_list_t *instanceMethods;
    struct method_list_t *classMethods;
    struct protocol_list_t *protocols;
    struct property_list_t *instanceProperties;
};
```

method_list_t 是一个存储 method_t 类型元素的容器。method_t 结构体的定义如下:
```
struct method_t {
    SEL name;
    const char *types;
    IMP imp;
};
```

还有一个结构体 category_list 用来存储所有的 category，它的定义如下:
```
struct locstamped_category_list_t {
    uint32_t count;
    locstamped_category_t list[0];
};
struct locstamped_category_t {
    category_t *cat;
    struct header_info *hi;
};
typedef locstamped_category_list_t category_list;
```

### Category原理之［解析过程］

对 Category 中方法的解析并不复杂，首先来看一下 attachCategories 的简化版代码:
```
static void attachCategories(Class cls, category_list *cats, bool flush_caches) {
    if (!cats) return;
    bool isMeta = cls->isMetaClass();

    method_list_t **mlists = (method_list_t **)malloc(cats->count * sizeof(*mlists));
    // Count backwards through cats to get newest categories first
    int mcount = 0;
    int i = cats->count;
    while (i--) {
        auto& entry = cats->list[i];

        method_list_t *mlist = entry.cat->methodsForMeta(isMeta);
        if (mlist) {
            mlists[mcount++] = mlist;
        }
    }

    auto rw = cls->data();

    prepareMethodLists(cls, mlists, mcount, NO, fromBundle);
    rw->methods.attachLists(mlists, mcount);
    free(mlists);
    if (flush_caches  &&  mcount > 0) flushCaches(cls);
}
```

首先，通过 while 循环，我们遍历所有的 category，也就是参数 cats 中的 list 属性。对于每一个 category，得到它的方法列表 mlist 并存入 mlists 中。
换句话说，我们将所有 category 中的方法拼接到了一个大的二维数组中，数组的每一个元素都是装有一个 category 所有方法的容器。这句话比较绕，但你可以把 mlists 理解为文章开头所说，旧版本的 objc_method_list **methodLists。**
在 while 循环外，我们得到了拼接成的方法，此时需要与类原来的方法合并:
```
auto rw = cls->data();
rw->methods.attachLists(mlists, mcount);
```

这两行代码读不懂是必然的，因为在 Objective-C 2.0 时代，对象的内存布局已经发生了一些变化。我们需要先了解对象的布局模型才能理解这段代码，剩余其他，可以参阅，参考1中的内容。
