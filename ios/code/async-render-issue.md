# 专题-异步渲染

http://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/

## 知识回顾

1. 屏幕显示图像的原理
2. 卡顿产生的原因和解决方案
    * CPU 资源消耗原因和解决方案
    * GPU 资源消耗原因和解决方案
3. 如何评测界面的流畅度

## 方案：AsyncDisplayKit

1. ASDK 的由来
2. ASDK 的资料
3. ASDK 的基本原理
4. ASDK 的图层预合成
5. ASDK 异步并发操作
6. Runloop 任务分发

## 方案：YYAsyncLayer

参考：[(源码解析之–YYAsyncLayer异步绘制)](http://ios.jobbole.com/86878/), 


## 案例

1. 微博 Demo 性能优化技巧