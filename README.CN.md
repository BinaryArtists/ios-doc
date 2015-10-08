这些准则是基于苹果现有的 [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)。
除非显式地与下面冲突，否则假设与所有苹果的准则一致。

## 空白

 * 制表符（Tabs），而不是 空格（spaces）。
 * 以另起一行，作为原文件的结束。
 * 自由地使用纵向空白，将代码分割成不同的逻辑块。
 * 不要有（行）末尾空白。
    * 甚至空行也不允许。

## 文档 和 组织

 * 所有方法声明都应该在文档中说明。
 * 注释应该规定在80个字符之内。
 * 注释的风格应该像这样：[Tomdoc](http://tomdoc.org/)-style。
 * Document whether object parameters allow `nil` as a value.
 * 使用 `#pragma mark -`s 将方法归类：功能组、协议实现，下面是一般性结构：（[fallenink](https://github.com/fallending)，更倾向于中间夹带`-`）

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

## Declarations

 * Never declare an ivar unless you need to change its type from its declared property.
 * Don’t use line breaks in method declarations.
 * Prefer exposing an immutable type for a property if it being mutable is an implementation detail. This is a valid reason to declare an ivar for a property.
 * Always declare memory-management semantics even on `readonly` properties.
 * Declare properties `readonly` if they are only set once in `-init`.
 * Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.
 * Declare properties `copy` if they return immutable objects and aren't ever mutated in the implementation. `strong` should only be used when exposing a mutable object, or an object that does not conform to `<NSCopying>`.
 * Avoid `weak` properties whenever possible. A long-lived weak reference is usually a code smell that should be refactored out.
 * Instance variables should be prefixed with an underscore (just like when implicitly synthesized).
 * Don't put a space between an object type and the protocol it conforms to.

```objc
@property (attributes) id<Protocol> object;
@property (nonatomic, strong) NSObject<Protocol> *object;
```

 * C function declarations should have no space before the opening parenthesis, and should be namespaced just like a class.

```objc
void GHAwesomeFunction(BOOL hasSomeArgs);
```

 * Constructors should generally return [`instancetype`](http://clang.llvm.org/docs/LanguageExtensions.html#related-result-types) rather than `id`.
 * Prefer helper functions (such as `CGRectMake()`) to C99 struct initialiser syntax.

```objc
  CGRect rect = CGRectMake(3.0, 12.0, 15.0, 80.0);
   ```

## Expressions

 * Don't access an ivar unless you're in `-init`, `-dealloc` or a custom accessor.
 * Use dot-syntax when invoking idempotent methods, including setters and class methods (like `NSFileManager.defaultManager`).
 * Use object literals, boxed expressions, and subscripting over the older, grosser alternatives.
 * Comparisons should be explicit for everything except `BOOL`s.
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

## Control Structures

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

## Exceptions and Error Handling

 * Don't use exceptions for flow control.
 * Use exceptions only to indicate programmer error.
 * To indicate errors, use an `NSError **` argument or send an error on a [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) signal.

## Blocks

 * Blocks should have a space between their return type and name.
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

## Literals

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

## Categories

 * Categories should be named for the sort of functionality they provide. Don't create umbrella categories.
 * Category methods should always be prefixed.
 * If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.
