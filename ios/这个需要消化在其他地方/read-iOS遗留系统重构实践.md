# 读《iOS遗留系统重构实践》

## 知识点

1. 分层（每层职责划分
   * 显示层：负责响应用户事件、调用业务层逻辑、数据呈现
   * 业务逻辑层：负责业务规划、数据处理
   * 数据访问层：负责封装数据库操作
   * 网络访问层：与 3 并列，负责网络请求、JSON解析（也就是数据交换的序列化和反序列化）
2. 架构
   * MVC
   * MVVM（该抽象的名称缩写，职责体现不够明确）
   * VIPER
3. 原则
   * 单一职责
   * 关注点分离
   * 高内聚低耦合

## 实践点

1. 解除View直接CoreData API调用（解耦：建立数据访问层，使用Realm实现）
    * 方案选型的风险点评测：
        1. 重写：
            1. 范围不可控——遗留系统的难点就在于牵一发而动全身，影响范围极广。稍不留神，重写的工作就会如野火燎原般蔓延开来，不可收拾。
            2. 长时间无法上线——在整个过程中，直到最后完成的那一刻之前，系统会处于一直不可用的状态。漫长的时间里，所有的新功能都被阻塞，不能交付。没有哪个产品团队能承担这样的结果。
        2. 特性分支：（把重写的工作放到分支上完成，其他人继续在主干上开发新特性，直到重写结束再合并回主干）
        3. 抽象分支（Branch by Abstraction）+特性开关（Feature Toggle）
            1. 当我们想要替换掉系统中的某个组件——名为X——时，首先为X组件创造一个抽象层，这一层里面可能会有大大小小若干接口或是协议，把系统中对X组件的访问都隔离在抽象层之下，系统只调用抽象的接口/协议，不会接触到具体的API实现。
￼![00001](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/00001.jpg)￼

		2. 特性开关
			a. 特性开关的实现方式：预编译参数、NSUserDefaults、服务器取值、第三方的A/B测试服务.
			![00002](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/00002.jpg)￼
		3. 数据迁移
			a. 这个无需多说，写个MigrationManager之类的类，用来把数据从CoreData中读出，写到Realm里面去。这个类大概要保留上三四个版本，等绝大部分用户都已经升级到新版本之后才会删掉。
			b. 在第一步建立数据访问层的时候，我们创建出了一个特别庞大的PersistenceService，它里面含有所有的数据访问方法。这只是为了方便切换而已，切换完成后，我们还是要根据访问数据的不同，建立一个个小的Repository，然后让ViewModel对象访问Repository读写数据，把PersistenceService删掉。
	![00002](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/imges/00002.jpg)￼

-------------------

## 启发点

第二部分实践点中的重构方案，可以参考。

-------------------

## 参考

  1. [iOS遗留系统重构实践](http://www.infoq.com/cn/articles/ios-legacy-codebase-refactor)
  2. [github-doc](https://github.com/BinaryArtists/not-just-code/blob/master/articles.ios/blog-reading/read-iOS%E9%81%97%E7%95%99%E7%B3%BB%E7%BB%9F%E9%87%8D%E6%9E%84%E5%AE%9E%E8%B7%B5.md)
