## [iOS 滑动性能优化](http://www.cocoachina.com/ios/20160120/15038.html)


### 一、 减少图层的Blend操作

展示半透明的view，设备会把当前图层和背景图层进行alpha叠加，这是一项很耗性能的一件事。如果动画中每一帧都做叠加，性能的损耗是很严重。

1. UIView的背景色避免使用clearColor
UIView记得设置成和SuperView相同的颜色
动作虽小，效果却好
尤其是在需要滑动的场景

2. 控件贴图避免使用带alpha的图片
视觉给出的贴图最好不带Alpha通道
如果必须使用Alpha，则主动去Alpha，提前和背景色合成为不含Alpha的图片
针对同一场景图片合成只需要做一次
一次合成，长期使用

3. UIImageView 使用时避免半透明
Disable alpha blending except where needed. Unless you are intentionally working with images that contain transparency (drawing UI elements, for example), you should generally mark the view as opaque by checking Opaque checkbox in the attributes inspector, or setting the opaque property on the view itself.

UIImageView的半透明取决于以下几项：

显示的图片
View的opaque属性的值
View的alpha值
View的背景色
An opaque view is expected to fill its bounds with entirely opaque content—that is, the content should have an alpha value of 1.0. If the view is opaque and either does not fill its bounds or contains wholly or partially transparent content, the results are unpredictable. You should always set the value of this property to NO if the view is fully or partially transparent.

规则如下：

当Opaque属性为YES的时候，imageView的alpha属性会被忽略，图层是否半透明取决于图片和imageView本身的背景色的叠加结果。
如果叠加结果图全部不透明，则图层不透明，不会触发blend操作。
如果叠加结果中出现半透明区域，则整个图层都会变成不透明，会触发blend操作。
如果Opaque属性为NO的时候，图层是否半透明取决于图片和imageView的multiplied叠加结果确定。
简单理解，如果可能尽量：
设置Opaque为YES
背景色设置为不含alpha的颜色
alpha值最好也是1(不透明)。
适用场景
通用优化规则，不会造成副作用


### 二、适当使用Rasterize

针对内容比较固定的Cell，建议采用光栅化，让Core Animation框架帮我们完成图层的混合，生成一个静态图，优化帧率。

适用场景
UITableView & UICollectionView & UIScrollView中内容变化不频繁的Cell
注：此优化需要Profile，使用Core Animation工具中的“ColorHitsGreenandMissesRed”工具调优
如果使用不当，可能适得其反



### 三、避免图片资源的重采样

Image views can perform two operations that are relatively expensive performance-wise: scaling the image and alpha compositing the image with lower layers.

减少图片资源的重采样是一个费时给力的过程，涉及到插值算法，以双线性插值为例，每插值一个点需要用到周围四个点的像素值，运算量可见一斑。

直接对于UIImageView设置一个大图，在实际展示的时候会在主线程完成重采样的过程，耗时耗内存。

如何避免？
网络图片资源
请求接口时，服务端根据场景返回尺寸尽可能接近展示的图片资源。  
此举既可以节省流量，又可以节省重采样的时间。
本地图片资源
有可能的话，针对不同场景放置多个尺寸的图片资源
针对应用场景生成一个适用于使用场景尺寸的图片资源，并在该场景中生成的图片
适用场景
　　所有需要使用图片的场景都可以使用此方案优化，无副作用。



### 总结

　　滑动性能优化这块儿涉及到的知识还是挺多的，不要盲目，过早的优化。使用Instrument找出瓶颈，然后合理使用不同的方案。性能优化有很多奇淫技巧，但通常做到上面几个大的点，基本上性能就能接受了。

　　对于TableView & Collection View这块儿还有一个很有效的优化手段，在快速滑动的时候，忽略中间快速闪过的Cell，直接借用UIScrollView的delegate判断加载滑动停止目标区域的Cell的内容。实践证明此方法效果还是很明显的，具体例子可以网上搜一下。

Profile -> 优化 -> Profile

　　一直重复上面的流程，直到达到预期为止。

　　最后重要的事情说三遍：

　　Profile一定请使用真机，最好使用Release模式，以达到最真实的效果。
　　Profile一定请使用真机，最好使用Release模式，以达到最真实的效果。
　　Profile一定请使用真机，最好使用Release模式，以达到最真实的效果。

### 更多参考资料：
[iOS Core Animation: Advanced Techniques](https://zsisme.gitbooks.io/ios-/content/index.html)
[UIImageView Class Reference](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class/)
[Image Resizing Techniques](http://nshipster.com/image-resizing/)
[OS 保持界面流畅的技巧](http://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)
[Alpha compositing](https://en.wikipedia.org/wiki/Alpha_compositing)
