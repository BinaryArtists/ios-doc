这些准则是基于苹果现有的 [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)。
除非显式地与下面冲突，否则假设与所有苹果的准则一致。

参考链接：
1. http://www.cocoachina.com/ios/20131129/7445.html
2. 

## 空白（Whitespaces）

 * 制表符（Tabs），而不是 空格（spaces）。
 * 每一行代码使用4个空格缩进。不使用tab缩进。
 * 以另起一行，作为原文件的结束。
 * 自由地使用纵向空白，将代码分割成不同的逻辑块。
 * 不要有（行）末尾空白。
    * 甚至空行也不允许。

## 文档（Documentations） 和 组织（Orgnizations）

 * 所有方法声明都应该在文档中说明。
 * 注释应该规定在80个字符之内。
 * 注释的风格应该像这样：[Tomdoc](http://tomdoc.org/)-style。
 * 文档中或可允许，对象参数用 `nil` 作为值。
 * 使用 `#pragma mark -`s 将方法归类：功能组、协议实现，下面是一般性结构：（[fallenink](https://github.com/fallending)，更倾向于中间夹带 `-` ）

```objc
#pragma mark - Initialization

- (void)initTableView {}

#pragma mark - Properties

@dynamic someProperty;

- (void)setCustomProperty:(id)value {}

#pragma mark - Lifecycle

+ (instancetype)objectWithThing:(id)thing {}
- (instancetype)init {}

#pragma mark - Action handler

- (IBAction)onAddItem:(id)sender {}

#pragma mark - Notification handler

#pragma mark - Drawing

- (void)drawRect:(CGRect) {}

#pragma mark - Another functional grouping

#pragma mark - UITableViewDelegate & UITableViewDataSource

#pragma mark - GHSuperclass

- (void)someOverriddenMethod {}

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```

## 声明（Declaration）

 * 绝不声明局部变量 ivar，除非你需要从某个属性cast到目标类型。（fixme：翻译有问题）
 * 倾向于暴露一个不可变的（immutable）属性，如果它的实现细节为可变的（mutable）。这是声明属性变量的有效理由。
 * 总是声明内存管理语法，甚至在`readonly` 属性。
 * 声明属性为 `readonly`，如果它们只在`-init`中设置了一次。
 * 绝不使用 `@synthesize`，除非编译器需要它。注意在协议（protocols）中的 optional 属性必须要有显式的synthesized，如下系统协议：

 ```objc
 @protocol CAMediaTiming
 
/* The begin time of the object, in relation to its parent object, if
 * applicable. Defaults to 0. */
@property CFTimeInterval beginTime;
 
// 省略余下

@end
 ```

 * 如果返回不可变（immutable）对象，同时在实现中没有被修改，则将属性声明为`copy`。当要暴露一个可变对象，或者该对象并未遵循 `<NSCopying>` 协议，则应该使用 `strong`。
 * 尽可能地避免使用 `weak`。一个常驻的弱引用，通常的代码味道是，被提取出来。
 * 实例变量应该下划线 `_` 前缀，如同隐式的 synthesized。
 * 不要爱对象类型和它所遵循的协议间留空格。

```objc
@property (attributes) id<Protocol> object;
@property (nonatomic, strong) NSObject<Protocol> *object;

@interface ClassName <Protocol>

@interface ClassName () <Protocol>
```

 * C函数声明中，在左括号前（the opening parenthesis）应该不留空格，并且函数名应该像类一样带有命名空间标识。

```objc
void GHAwesomeFunction(BOOL hasSomeArgs);
```

 * 构造通常应该返回 [`instancetype`](http://clang.llvm.org/docs/LanguageExtensions.html#related-result-types) rather than `id`.
 * 与其使用 C99 结构初始化语法（struct initialiser syntax），不如使用辅助函数(such as `CGRectMake()`)。

```objc
  CGRect rect = CGRectMake(3.0, 12.0, 15.0, 80.0);
```

## 表达式（Expressions）和 间距（）

 * 除非在`-init`, `-dealloc` 或者 定制的访问器中，否则不要直接访问成员变量。

```objc
// Objective-C运行时定义了几种重要的类型
// ================================
// Class：定义Objective-C类
// Ivar：定义对象的实例变量（成员变量，与‘属性’作比较），包括类型和名字。
// Protocol：定义正式协议。
// objc_property_t：定义属性。叫这个名字可能是为了防止和Objective-C 1.0中的用户类型冲突，那时候还没有属性。
// Method：定义对象方法或类方法。这个类型提供了方法的名字（就是**选择器**）、参数数量和类型，以及返回值（这些信息合起来称为方法的**签名**），还有一个指向代码的函数指针（也就是方法的**实现**）。
// SEL：定义选择器。选择器是方法名的唯一标识符。
// IMP：定义方法实现。这只是一个指向某个函数的指针，该函数接受一个对象、一个选择器和一个可变长参数列表（varargs），返回一个对象
```

 * 使用点语法，当调用幂等方法（多次调用和一次调用返回的结果相同），包括 setters 和 类方法(like `NSFileManager.defaultManager`)。

```objc
view.backgroundColor = [UIColor redColor]; // good

[view setBackgroundColor:nil]; // bad
```

 * 使用对象常量，加括号表达式，下标，在旧的方案。（百度翻译）
 * 所有比较应该是显式行为，除了布尔值 `BOOL`s。
 * 倾向于使用正向的比较，而非负向的。（判断相等性，而非不等）
 * 长的三元运算符应使用圆括号括起来。三元运算符仅用于赋值和做参数。

```objc
Blah *a = (stuff == thing ? foo : bar);
```

* 合并的nil三元运算符应该尽量避免。

```objc
Blah *b = thingThatCouldBeNil ?: defaultValue;
```

 * 二元运算符和参数之间需要放置一个空格，一元运算符、强制类型转换和参数之间不放置空格。关键字之后圆括号之前需要放置一个空格。

```c
void *ptr = &value + 10 * 3;
NewType a = (NewType)b;

for (int i = 0; i < 10; i++) {
    doCoolThings();
}
```
 * 每一行代码不要超过100个字符。
 * 如果一个方法内有多个功能区域，可以使用空行分隔功能区域。
 * 每一个方法之前都有一个99字符宽的注释行，注释行相对于使用空行更能提高代码的辨识度，当一行代码很长的时候，注释行也起到了越界检测的作用。（maybe）

## 控制结构（Control Structures）

 * 如果有`else`，则`else` `if` 的代码块，都需要用花括号包围。如果`if`只跟单行语句，则它保持与`if`同一行。
 * 所有的花括号应，与关联的语句同起一行，新起一行结束。
 * 在关键字后、圆括号前，都空格。
 * Return 和 break 早起。
 * 圆括号与其中的内容之间紧邻，不空格。

```objc
if (somethingIsBad) return;

if (something == nil) {
	// do stuff
} else {
	// do other stuff
}

{
    // do stuff on function one
}

{
    // do stuff on function two
}
```

## 异常（Exceptions） 和 错误处理（Error Handling）和 断言（Assertion）

 * 绝不在，流程控制语句中使用异常（NSException）。
 * 只应该，用异常指示程序员犯的错误。
 * 指示错误，在[ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) 的信号上发送错误，或者使用 `NSError **` 参数。

     1. 开发版中，尽可能用断言，或调试辅助代码，代替用回车键逃避的问题。
     2. 用断言，去检查编程中，所做的各种假定。

## 块（Blocks）

 * 块（Blocks）的定义或声明中，在返回类型和块名字之间应该留有空格。
 * 块定义应该尽可能省略其返回类型。
 * 块定义如果无参（`void`），则可以省略。
 * 块类型参数可以不必命名，除非这个块被立即初始化。

```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^ id (id args) {
    // do some things
};
```

## 常量（Literals）

 * 给数字指定特定的类型。(例如，偏向于使用 `5` to `5.0`, and `5.3` to `5.3f`)。
 * 数组和字典类型的字面值的方括号两边各放置一个空格。

```objc
NSArray *theShit = @[ @1, @2, @3 ];
```

 * 字典字面值的键和冒号之间没有空格，冒号和值之间有一个空格。

```objc
NSDictionary *keyedStuff = @{ GHDidCreateStyleGuide: @YES };
```

 * 长的字面值应被拆分为多行。 (可选择使用，逗号来终止):

```objc
NSArray *theStuff = @[
    @"Got some long string objects in here.",
    [AndSomeModelObjects too],
    @"Moar strings."
];

NSDictionary *keyedStuff = @{
    @"this.key": @"corresponds to this value",
    @"otherKey": @"remoteData.payload",
    @"some": @"more",
    @"JSON": @"keys",
    @"and": @"stuff",
};
```

## 类别（Categories）

 * 类别应该依据其提供的功能命名，不要创建总括类别。
 * 类别方法应该加以前缀。Category methods should always be prefixed.
    局限性：
        1.类别只能扩充方法，而不能扩充成员变量。
        2.名称冲突，即类别中的方法与现有方法重名。当发生名称冲突时，类别具有更高的优先级。可以在自己的类别方法名中增加一个前缀，以确保不发生名称冲突。

```objc
@interface UIButton (WCProgress)

-(void)WC_setUpCustomButton; // 集中维护的项目：有精力的，应该避免这样；分布维护的项目，则可以如此，避免冲突。

@end
```

 * 如果为了子类化或者单元测试，需要暴露私有方法，创建特定类别： `Class+Private`。

## 命名（Names）

 * 三种编程命名规则，统一一种风格，当前苹果推荐固然是，同其api一致

    1. 匈牙利命名：
    开头字母用变量类型的缩写，其余部分用变量的英文或英文的缩写，要求单词第一个字母大写。
    For example: long lsum = 0;"l"是类型的缩写；

    2. 小驼峰式：(little camel-case)
    第一个单词首字母小写，后面其他单词首字母大写。
    For example: string firstName = string.Empty;

    3. 大驼峰式：(big camel-case)(帕斯卡（pascal）命名法)
    每个单词的第一个字母都大写;
    For example：string FirstName = string.Empty;

    4. 下划线风格：（underscore-case）
    每个单词用下划线相连
    For example：string first_name = string.Empty;

 * 一个方法的命名首先描述返回什么，接着是什么情况下被返回。方法签名中冒号的前面描述传入参数的类型。以下类方法和实例方法命名的格式语法：
```objc
[object/class thing+condition];
[object/class thing+input:input];
[object/class thing+identifer:input];
```
 * Cocoa命名举例：
```objc
realPath    = [path     stringByExpandingTildeInPath];
fullString  = [string   stringByAppendingString:@"Extra Text"];
object      = [array    objectAtIndex:3];

// 类方法
newString   = [NSString stringWithFormat:@"%f",1.5];
newArray    = [NSArray  arrayWithObject:newString];

// good
recipients  = [email    recipientsSortedByLastName];
newEmail    = [CDCEmail emailWithSubjectLine:@"Extra Text"];
emails      = [mailbox  messagesReceivedAfterDate:yesterdayDate];
```

 * 当需要获取对象值的另一种类型的时候，方法命名的格式语法如下：
```objc
[object adjective+thing];
[object adjective+thing+condition];
[object adjective+thing+input:input];

// good
capitalized = [name    capitalizedString];
rate        = [number  floatValue];
newString   = [string  decomposedStringWithCanonicalMapping];
subarray    = [array   subarrayWithRange:segment];
```
 
 * 方法签名尽量做到含义明确。
```objc
// bad：
-sortInfo  // 是返回排序结果还是给info做排序
-refreshTimer  // 返回一个用于刷新的定时器还是刷新定时器
-update  // 更新什么，如何更新

// good
-currentSortInfo      // "current" 清楚地修饰了名词SortInfo
-refreshDefaultTimer  // refresh是一个动词。
-updateMenuItemTitle  // 一个正在发生的动作
```
 
 * 方法类型修饰符+/-后要放置一个空格，各参数名之间也要放置一个空格。
```objc
// good
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

 * 如果方法的命名特别长，将方法名拆分成多行。
```objc
// good
color = [NSColor colorWithCalibratedHue: 0.10
                             saturation: 0.82
                             brightness: 0.89
                                  alpha: 1.00];
```
 
 * 不要将私有的实例变量和方法声明在头文件中，应将私有变量和方法声明在实现文件的类扩展内。

```objc
// bad:
//MyViewController.h文件
@interface MyViewController : UIViewController<
 UITalbeViewDataSource,
 UITableViewDelegate> {
 @private:
  UITableView *_myTableView;  // 私有实例变量
}

// 内部使用的属性
@property (nonatomic,strong) NSNumber *variableUsedInternally;
- (void)sortName;  // 只用于内部使用的方法
@end

// good:
//MyViewController.m文件使用类扩展
@interface MyViewController()<
 UITalbeViewDataSource,
 UITableViewDelegate> {
  UITableView *_myTableView;
// 外部需要访问的实例变量声明为属性，不需要外部访问的声明为实例变量
  NSNumber * variableUsedInternally;
}

// 从Xcode4.3开始，可以不写方法的前置声明，Interface Builder和Storyboard仍然可以找到方法的定义
@end
```

 * 构造函数通常应该返回实例类型而不是id类型
 ```objc
- (instancetype)init;
 ```

## 协议 (Protocol) 与 代理 (Delegate)
 * 除了继承一个类或实现一个协议，否则在头文件中仅使用类声明@class指令，不用#import导入类头文件。
 * 如果一个delegate只有几个方法，比如只是提交和取消，推荐使用block编写动作响应代码。
 * 由于代理方法的声明一般都很长，所以必须将代理对象和其他的协议对象放在实例变量定义的下面，否则实例变量定义的对齐方式将会被打乱掉。
 * 当需要实现多个协议的时候，将每一个协议名拆分到单独的行。
 
```objc
@interface CustomModelViewController : TTViewController <

  TTModelDelegate,

  TTURLRequestDelegate

> {
```

 * 缩略词: (pdf, xml, html, url, rtf, http, tiff, png, gif, rom, rgb, ftp)

## 参数 (arguements)
 * 方法参数名前一般使用的前缀包括“the”、“an”、“new”。
```objc
    - (void)setTitle:(NSString *)aTitle;
    - (void)setName:(NSString *)newName;
    - (id)keyForOption:(CDCOption *)anOption;
    - (NSArray *)emailsForMailbox:(CDCMailbox *)theMailbox;
    - (CDCEmail *)emailForRecipients:(NSArray *)theRecipients;
```

## 变量 (Variable)
 * 变量的命令应尽量做到自描述。除了在for()循环语句中，单字母的变量应该避免使用（如i,j,k等）。
   一般循环语句的当前对象的命名前缀包括“one”、“a/an”。对于简单的单个对象使用“item”命名。
```objc
for (i = 0; i < count; i++) {
    oneObject = [allObjects objectAtIndex: i];
    NSLog (@"oneObject: %@", oneObject);
}

NSEnumerator *e = [allObjects objectEnumerator];
id item;
while (item = [e nextObject])
      NSLog (@"item: %@", item);
```
 
 * 指针变量的星号指示符应该紧靠变量，比如NSString *text,而不是NSString* text或NSString * text。
 
 * 尽量的使用属性而非实例变量。
   除了在初始化方法（init，initWithCoder：等）、dealloc方法以及自定义setter与getter方法中访问属性合成的实例变量，其他的情况使用属性进行访问。
 
```objc
// good:
@interface RNCSection: NSObject
@property (nonatomic) NSString *headline;
@end

// bad:
@interface RNCSection : NSObject {
    NSString *headline;
}
```

 * 当你使用@synthesize指令时，编译器会自动为你创建一个下划线_开头的的实例变量，所以不需要同时声明实例变量和属性。
```objc
// bad:
@interface RNCSection : NSObject {
    NSString *headline;
}
@property (nonatomic) NSString *headline;
@end
 
// good:
@interface RNCSection: NSObject
@property (nonatomic) NSString *headline;
@end
```
 
 * 不要使用@synthesize除非是编译器需要。注意在@protoco协议中的@optional可选属性必须被显式地使用@synthesize指令合成属性。
 * 变量命名应该尽可能做到自描述：
```objc
// good:
UIButton *settingsButton;
 
// bad:
UIButton *setBut;
```

 * 对于NSString、NSArray、NSNumber或BOOL类型，变量的命名一般不需要表明其类型。
```objc
// good:
NSString       *accountName;
NSMutableArray *mailboxes;
NSArray        *defaultHeaders;
BOOL           userInputWasUpdated;

// bad:
NSString       *accountNameString;
NSMutableArray *mailboxArray;
NSArray        *defaultHeadersArray;
BOOL           userInputWasUpdatedBOOL;
```
 
 * 如果变量不是以上基本常用类型，则变量的命名就应该反映出自身的类型。但有时仅需要某些类的一个实例的情况下，那么只需要基于类名进行命名。
```objc
NSImage              *previewPaneImage; 
NSProgressIndicator  *uploadIndicator; 
NSFontManager        *fontManager;       // 基于类名命名
```

 * 大部分情况下，NSArray或NSSet类型的变量只需要使用单词复数形式（比如mailboxes），不必在命名中包含“mutable”。如果复数变量不是NSArray或NSSet类型，则需要指定其类型。
```objc
NSDictionary * keyedAccountNames;
NSDictionary * messageDictionary;
NSIndexSet   * selectedMailboxesIndexSet;
```

 * 由于Objective-C不支持名字空间，为了防止出现命名空间的冲突，在类名和常类型变量名前添加一个由三个大写的字母组成的前缀（如RNC），对于Core Data实体名则可以忽略此规则。如果你子类化了标准的Cocoa类，将前缀和父类名合并是一个很好的做法。如继承UITableView的类可命名为RNCTableView。
 * 常类型变量名的书写风格采用驼峰式大小写（第一个单词的首字母小写，其余单词的第一个字母大写。如firstName而不是first_name或firstname。），并使用关联的类名作为其命名前缀，
 
```objc
// good:
static const NSTimeInterval RNCArticleViewControllerNavigationFadeAnimationDuration = 0.3;
 
// bad:
static const NSTimeInterval fadetime = 1.7;
```

## 下划线
 * 使用属性的时候，实例变量应该使用self.
 * 进行访问和设值。局部变量的命令不要包含下划线。实例变量的命名必须使用下划线_作为前缀，这样可以缩小Xcode自动完成的选项取值范围。

## 注释
 * 使用javadoc风格的文档注释语法。注释的第一行是对注释API的总结，随后的注释行是对代码更多细节的解释。
```objc
/**
 * The maximum size of a download that is allowed.
 *
 * If a response reports a content length greater than the max * will be cancelled. This is helpful for preventing excessive memory usage.
 * Setting this to zero will allow all downloads regardless of size.
 *
 * @default 150000 bytes
 */
@property (nonatomic) NSUInteger maxContentLength;
```

 * init与dealloc
 * dealloc方法应该被放置在实现方法的顶部，直接在@synthesize或@dynamic语句之后。init方法应该被放置在dealloc方法的下面。
 * init方法的结构看上去应该像这样：
```objc
- (instancetype)init {
    self = [super init]; // or call the designated initalizer
    if (self) {
        // Custom initialization
    }
    return self;
}
```





