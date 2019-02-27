## 移动端数据库


未整理. Database
    1. https://github.com/Todd2010/Realm-Document-Translation-And-Extension
    2. https://github.com/meech-ward/SMWRealm
    3. https://github.com/bigfish24/RealmSubqueryHelper
    4. https://github.com/bofrese/RealmEasy
    5. https://github.com/kishikawakatsumi/Realm-Hands-On
    6. https://github.com/Roobiq/RBQRealmNotificationManager
    7. https://github.com/yusuga/YSRealmStore
    8. https://github.com/Roobiq/RBQSafeRealmObject

  * [IOS-数据库模块搭建方案](http://www.jianshu.com/p/ef895925c270)，要点如下：
    > 笔者建议自行搭建数据库管理类，同时配合成熟的开源ORM框架快速搭建数据库模块。
    > 数据库 多线程问题
    > 1. 定义DAO基类ZZBaseDAO
    > 2. ZZBaseDAO实现类，通过调用NSObject的 setValuesForKeysWithDictionary和valueForKey实现NSDictionary和entity对象间转换
    > 3. 处理数据库中特殊字段如id等与oc关键字冲突的情况：定义函数setValue:forUndefinedKey，实现特殊数据库字段与entity中property的映射。

### 设计

  * 类概要：数据库管理类＋db可选方案
  * 类设计：
    1. Serialization & Deserialization
    2. Connections
      > Concurrency issues

    3. Transactions
      > SQLite itself is also transactional

  * 特性设计
    * ORM
      > Java对象映射到SQLite数据库的表单

### 可选方案

1. FMDB
2. CoreData
3. Realm
4. [yapstudios/YapDatabase](https://github.com/yapstudios/YapDatabase)
5. NoSQL：
  * [couchbase/couchbase-lite-ios](https://github.com/couchbase/couchbase-lite-ios), 轻量、嵌入式、可同步的NoSQL数据库引擎。
  * [couchbaselabs/TouchDB-iOS](https://github.com/couchbaselabs/TouchDB-iOS), Apache CouchDB是一种新型的数据库管理系统，这种系统通常叫作NoSQL。NoSQL是在2009年初就浮现出并开始流行起来的，即是non-SQL的数据库。NoSQL是一种非关系型数据存储系统，这打破了长期以来以关系型数据库和ACID特性主导的数据库世界。这种系统的数据存储不需要固定的表

### 其他

1. ActiveRecord（BeeFramework）

属于ORM(Object relative map)层

来自“百度知道”：Active Record是一种领域模型模式，特点是一个模型类对应关系型数据库中的一个表，而模型类的一个实例对应表中的一行记录。Active Record和Row Gateway十分相似，但前者是领域模型，后者是一种数据源模式。关系型数据库往往通过外键来表述实体关系，Active Record在数据源层面上也将这种关系映射为对象的关联和聚集。在Martin Fowler2003年出版的《企业应用架构模式》一书中详细叙述并命名了本模式。[1]

其中：Magical Record 灵感来源于Ruby on Rails' ActiveRecord 系统（wrapper of CoreData）
