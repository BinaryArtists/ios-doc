## 移动端数据库

  * [IOS-数据库模块搭建方案](http://www.jianshu.com/p/ef895925c270)

### 设计

  * 类概要：数据库管理类＋db可选方案
  * 类设计：
    1. Serialization & Deserialization
    2. Connections
      > Concurrency issues

    3. Transactions
      > SQLite itself is also transactional

### 可选方案

1. FMDB
2. CoreData
3. Realm

### 其他

1. ActiveRecord（BeeFramework）

属于ORM(Object relative map)层

来自“百度知道”：Active Record是一种领域模型模式，特点是一个模型类对应关系型数据库中的一个表，而模型类的一个实例对应表中的一行记录。Active Record和Row Gateway十分相似，但前者是领域模型，后者是一种数据源模式。关系型数据库往往通过外键来表述实体关系，Active Record在数据源层面上也将这种关系映射为对象的关联和聚集。在Martin Fowler2003年出版的《企业应用架构模式》一书中详细叙述并命名了本模式。[1]

其中：Magical Record 灵感来源于Ruby on Rails' ActiveRecord 系统（wrapper of CoreData）
