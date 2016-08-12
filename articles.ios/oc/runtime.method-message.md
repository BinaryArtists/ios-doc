## runtime 消息机制

  * [runtime那些事儿（消息机制）](http://www.cocoachina.com/ios/20160301/15494.html)
  * [Runtime初涉之消息转发](http://www.cocoachina.com/ios/20151015/13769.html)

### 消息传递

[image](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/message_send_funct_invoke.png)

相关函数：
  * 对象在收到无法解读的消息后，首先会调用所属类的
  ```
  + (BOOL)resolveInstanceMethod:(SEL)sel
  ```

  > 这个方法在运行时，没有找到SEL的IML时就会执行。这个函数是给类利用class_addMethod添加函数的机会。

  ```
  OBJC_EXPORT BOOL class_addMethod(Class cls, SEL name, IMP imp,  const char *types)
  ```

  *

### 消息转发

当向某个对象发送一条消息时，若该对象的方法列表以及它相应继承链上的方法列表都无法找到以该消息选择子作为key的方法实现时，则会触发消息转发机制。

1、动态方法解析
```
+ (BOOL)resolveInstanceMethod:(SEL)sel;
```
首先，当接受到未能识别的选择子时，运行时系统会调用该函数用以给对象一次机会来添加相应的方法实现，如果用户在该函数中动态添加了相应方法的实现，则跳转到方法的实现部分，并将该实现存入缓存中，以供下次调用。

2、备援接收者
```
- (id)forwardingTargetForSelector:(SEL)aSelector;
```
如果运行时在消息转发的第一步中未找到所调用方法的实现，那么当前接收者还有第二次机会进行未知选择子的处理。这时运行期系统会调用上述方法，并将未知选择子作为参数传入，该方法可以返回一个能处理该选择子的对象，运行时系统会根据返回的对象进行查找，若找到则跳转到相应方法的实现，则消息转发结束。

3、完整的消息转发
```
- (void)forwardInvocation:(NSInvocation *)anInvocation;
```
当运行时系统检测到第二步中用户未返回能处理相应选择子的对象时，那么来到这一步就要启动完整的消息转发机制了。该方法可以改变消息调用目标，运行时系统根据所改变的调用目标，向调用目标方法列表中查询对应方法的实现并实现跳转，这种方式和第二步的操作非常相似。当然你也可以修改方法的选择子，亦或者向所调用方法中追加一个参数等来跳转到相关方法的实现。

最后，如果消息转发的第三步还未能处理该未知选择子的话，那么最终会调用NSObject类的如下方法用以异常的抛出，表明该选择子最终未能处理。

```
- (void)doesNotRecognizeSelector:(SEL)aSelector;
```

下面附上完整的消息转发流程图：
[image](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/msg_dispatch_diagram.png)

### 动态方法解析


### 方法缓存
