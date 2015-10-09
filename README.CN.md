这些准则是基于苹果现有的 [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)。
除非显式地与下面冲突，否则假设与所有苹果的准则一致。

http://www.cocoachina.com/ios/20131129/7445.html

## 空白（Whitespaces）

 * 制表符（Tabs），而不是 空格（spaces）。
 * 以另起一行，作为原文件的结束。
 * 自由地使用纵向空白，将代码分割成不同的逻辑块。
 * 不要有（行）末尾空白。
    * 甚至空行也不允许。

## 文档（Documentations） 和 组织（Orgnizations）

 * 所有方法声明都应该在文档中说明。
 * 注释应该规定在80个字符之内。
 * 注释的风格应该像这样：[Tomdoc](http://tomdoc.org/)-style。
 * Document whether object parameters allow `nil` as a value.
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

 * C 函数声明在左括号前（the opening parenthesis）应该不留空格，应该像一个类一样前置声明。

```objc
void GHAwesomeFunction(BOOL hasSomeArgs);
```

 * 构造通常应该返回 [`instancetype`](http://clang.llvm.org/docs/LanguageExtensions.html#related-result-types) rather than `id`.
 * 与其使用 C99 结构初始化语法（struct initialiser syntax），不如使用辅助函数(such as `CGRectMake()`)。

```objc
  CGRect rect = CGRectMake(3.0, 12.0, 15.0, 80.0);
```

## 表达式（Expressions）

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
 * Use object literals, boxed expressions, and subscripting over the older, grosser alternatives.
 * 所有比较应该是显式行为，除了布尔值 `BOOL`s。
 * Prefer positive comparisons to negative.
 * Long form ternary operators should be wrapped in parentheses and only used for assignment and arguments.

```objc
Blah *a = (stuff == thing ? foo : bar);
```

* Short form, `nil` coalescing ternary operators should avoid parentheses.

```objc
Blah *b = thingThatCouldBeNil ?: defaultValue;
```

 * Separate binary operands with a single space, but unary operands and casts with none:

```c
void *ptr = &value + 10 * 3;
NewType a = (NewType)b;

for (int i = 0; i < 10; i++) {
    doCoolThings();
}
```

## 控制结构（Control Structures）

 * Always surround `if` bodies with curly braces if there is an `else`. Single-line `if` bodies without an `else` should be on the same line as the `if`.
 * All curly braces should begin on the same line as their associated statement. They should end on a new line.
 * Put a single space after keywords and before their parentheses.
 * Return and break early.
 * No spaces between parentheses and their contents.

```objc
if (somethingIsBad) return;

if (something == nil) {
	// do stuff
} else {
	// do other stuff
}
```

## 异常（Exceptions） 和 错误处理（Error Handling）和 断言（Assertion）

 * 绝不在，流程控制中使用异常。
 * 只应该，用异常指示程序员犯的错误。
 * 指示错误，在[ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) 的信号上发送错误，或者使用 `NSError **` 参数。

     1. 开发版中，尽可能用断言，或调试辅助代码，代替用回车键逃避的问题。

## 块（Blocks）

 * 块（Blocks）应该在 should have a space between their return type and name.
 * Block definitions should omit their return type when possible.
 * Block definitions should omit their arguments if they are `void`.
 * Parameters in block types should be named unless the block is initialized immediately.

```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^ id (id args) {
    // do some things
};
```

## 常量（Literals）

 * Avoid making numbers a specific type unless necessary (for example, prefer `5` to `5.0`, and `5.3` to `5.3f`).
 * The contents of array and dictionary literals should have a space on both sides.
 * Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

``` objc
NSArray *theStuff = @[ @1, @2, @3 ];

NSDictionary *keyedStuff = @{ GHDidCreateStyleGuide: @YES };
```

 * Longer or more complex literals should be split over multiple lines (optionally with a terminating comma):

``` objc
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

 * Categories should be named for the sort of functionality they provide. Don't create umbrella categories.
 * Category methods should always be prefixed.
 * If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.

### 补充

## 命名（Names）

 * 三种编程命名规则，统一一种风格，当前苹果推荐固然是，同其api意志

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

 * ....