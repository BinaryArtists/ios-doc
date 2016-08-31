## 重业务模块的页面跳转问题

### 评估考察点

  1. 页面、模块耦合度
  2. 调试顺畅度
  3. 装饰器模式的灵活性（比如装饰个下面的技术点），也就是扩展灵活性

### 技术点

  1. 跳转动画
  2. 内链、外链

### 方案一：route by URL

  * 代表作：
    1. [URLManager](https://github.com/gaosboy/urlmanager)
    2. [DarielChen/DCURLRouter](https://github.com/DarielChen/DCURLRouter)
    3. [oenius/DarwinNativeRouter](https://github.com/oenius/DarwinNativeRouter)

  * 优点：
    1.

  * 缺点
    1. 不容易梳理逻辑（可以使用宏生成协议，技巧性）
    2. URL称为一个内部及与外部握手的协议，容易变得异构化严重（异构化，各模块内部结构迥异）
    3. 容易产生不符合W3C的URL标准

### 方案二：route by dependency injection

  * 代表作：
    1.

  * 优点：
    1.

  * 缺点
