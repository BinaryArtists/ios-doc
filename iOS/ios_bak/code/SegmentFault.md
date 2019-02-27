## [SegmentFault](https://github.com/gaosboy/iOSSF) 源码分析

### 基于版本

略

### 架构模式

  1. 常规的MVC
  2. 基于架构的项目目录：
  ```
  |-- SegmentFault
    |-- Views
      |-- Cells
    |-- DataCenters
    |-- Services
    |-- Tools
    |-- ViewControllers
      |-- Nav
      |-- Base
    |-- Delegates
    |-- Supports
      |-- JSONKit
      |-- URLManager
      |-- Kache （自主开发的缓存控件）（fallenink: 比较火的，是TMCache，YYCache）
      |-- AF
      |-- GTM
      |-- SlimeRefresh
  ```

### 是否有框架层，以及详细？

值得提出的有几点，如下：

  1. DateCenter，其实就是个HttpClient
  2. Serivce层，按领域模型来区分的
  3. 在Support里面有几个：Kache，URLManager，

### 重要的模块

无

### 有趣的方案或者代码段

无
