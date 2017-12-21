# SqLite 调优

*官方文档*
* [wal](https://www.sqlite.org/wal.html)

*其他开发者的博客*

* [【Dev Club 分享第七期】微信iOS SQLite源码优化实践](http://www.cocoachina.com/ios/20170622/19603.html)
* [sqlite 数据库性能调优](http://www.cocoachina.com/ios/20170622/19603.html)
* [iOS - SqLite 数据库服务层优化过程](http://www.cocoachina.com/ios/20170622/19603.html)
* [iOS 平台 SQLite 性能优化](https://www.logcg.com/archives/2316.html)
* [SQLite这么娇小可爱，不多了解点都不行啊](http://www.cocoachina.com/ios/20150824/13169.html)
* [iOS开发之SQLite安全问题解析之SQLite的锁机制和WAL技术](http://blog.csdn.net/zhonggaorong/article/details/52179696)
* [Performance Optimization of SQLite on iOS with Xamarin](http://blog.thomasbandt.de/39/2433/de/blog/performance-optimization-of-sqlite-on-ios-with-xamarin.html)

### 场景 - 优化一定要有的需求点

有个SDK的数据库读写挺慢的，写入 1000 条数据耗时 18.5 秒，虽然放在了异步线程做，但感觉还慢了点。另外手机的硬件配置比较低，考虑到电池续航，大小等等因素不可能做得很快。硬件改不了，考虑从代码层面优化。

一个问题，假设写入 1000 条数据时，又触发了读数据，要上传到服务器，还得等 18.5 秒，这个过程中，APP进程可能被 kiil 掉，用户手动关闭 APP 后，其进程 5 秒钟后结束。导致数据没写到库里就被 kill 了，数据丢失。

## 优化过程

用 FMDB，基于 sqlite 的数据库。

### 1.改配置

用 WAL 模式
```
PRAGMA journal_mode = WAL
PRAGMA journal_mode = DELETE
```
WAL 模式：事务写的时候，若提交，则写到 WAL 文件中，若回滚就直接不写文件了，sqlite 按时把 WAL 的内容合并到数据库中。

DELETE 模式：事务写的时候，先备份一份数据到 old 文件，若提交，则删除 old 文件，若回滚，则把 old 内容还原到原来的位置。

显然 WAL 模式对写的性能更快。读的话得读多个文件。

WAL模式测试结果

## 用多线程模式

```
PRAGMA SQLITE_THREADSAFE = 0   // 单线程
PRAGMA SQLITE_THREADSAFE = 1   // 多线程串行
PRAGMA SQLITE_THREADSAFE = 2   // 多线程并发
```
SQLITE_THREADSAFE = 2 后，FMDB不加锁，性能快了，但需自己保证数据库访问的线程安全。

### 2.去掉业务层 SQL 注入检查

为了防止 SQL 注入，旧代码检查用户字段和数据库字段是否匹配，用了个 O(n^2) 复杂度的方法，看着就觉得慢。

后来改成了哈希查找的方法，快了一点。

后来发现 FMDB 已经做了 SQL 防注入了。

使用 ? 通配符，让 FMDB 去匹配，如果 name 是个 SQL 语句，它将被当做字符串插入数据库中，不会被执行到。
```
[db executeUpdate:@"insert into student(name) values(?)", @"hehe"];
```
不要自己去拼接 SQL 语句，除非自己做了防 SQL 注入检查。

如果是自己拼接的，别人传个 name = " 'a') delete from student --' "
```
char *name = " 'a')  delete from student --' ";
"insert into student(name) values(%s)", name;
变成 
insert into student(name) values('a')  delete from student --')
整理一下
insert into student(name) values('a')  
delete from student 
--')
```
整个表都被清空了。

### 3.多条语句合并成一个事务提交

复习一下事务的四大特性，原子性，一致性，隔离性，永久性

开启和结束事务是比较耗时的。在 WAL 模式下，事务要写 WAL 文件，DELETE 模式下，事务要先复制一份 old 旧数据，然后提交或回滚了，还要删掉或者还原就数据。

能合并成事务提交的语句就合并，性能会高一些。因为每条查询语句，数据库默认会开启一个隐式事务。像下面这样
```
for (int i=0; i<10000; i++) 
{
    [db beginTransaction];    // 数据库会默认开启，你不写就默认开启
    insert into student(name) values('caokun')
    [db commit];              // 数据库会默认开启
}
```

自己开启事务，即显式事务
```
[db beginTransaction];    // 自己手动写的
for (int i=0; i<10000; i++) 
{
    insert into student(name) values('caokun')
}
[db commit];              // 自己手动写的
```
合并成事务

假设调用层会不定时的发来 SQL 查询语句，我把这些语句加入到一个串行队列，串行的执行。

收到一条查询请求，等待 25 ms，如果 25 ms 内没请求来，那么执行它。

如果 25 ms 内又来了另一个请求，那么重新等待 25 ms，直到超了 25 ms，然后合并成一个事务执行。

如果一直来请求，不就永远也执行不了了？

设置最大条数，比如超过 1000 个请求了，就把前 1000 个打包成一个事务执行。

或者超过一定时间，比如 2 秒，就打包事务执行。

或者提供批量查询的接口。业务层有些频繁写库，但是又没办法用批量写库的。比如当用户快速滑动屏幕，显示一个 cell，就插入一条记录，没办法用批量查询的接口。

4.读与读并发

第3步说了用个串行队列去管理所有请求。请求只有 select-读, update-写, delete-写, insert-写，所以要么是读，要么是写。

所以队列里是这样的：读读写写读写读写读读读读...

读跟读之间都是串行的。但读不改变数据完整性，所以读跟读是可以并发的。进一步提高了读的效率。

由于业务需求要保证一致性，即写了，要马上能读出来，不能有延迟，所以没办法把读跟写做成并发的了，必须等写完，才能读，不然很可能读不到刚刚写入的。

所以要做成这样的：写跟写同步，写跟读同步，读跟读并发。

这就变成了多个读者写者的问题了

允许多个读者读，多个写者写，但是有写者在写的时候，是不能读的，有读者在读的时候，是不能写的。

读者写者已有解决的办法。
```
定义 sem_cache 是同步共享区访问的信号量，应用中共享区即是数据库
定义 count 读者的计数，记录有几个读者在读
定义 sem_count 是同步计数器的访问的信号量
 
// 多个写者线程
void writer() 
{
    while (1) 
    {
        P(sem_cache);    // 尝试进入共享区写
        write_data();    // 写共享区
        V(sem_cache);    // 释放信号
    }
}
 
// 多个读者线程
void reader() 
{
    while (1)
    {
        P(sem_count);  // 读者尝试修改 count 进入共享区
        if (count == 0) 
        {
            // count == 0 说明是第一个读者进来，那么把共享区锁住，不让其他人访问
            P(sem_cache);
        }
        count++;    // 加上自身
        V(sem_count);  // count 访问完了，释放
 
        read_data();  // 读共享区
 
        P(sem_count);  // 读者尝试修改 count 进入共享区       
        count--;  // 自身离开
        if (count == 0) 
        {
            // count == 0 说明是最后一个读者出去，那么把共享区释放，让其他人访问
            V(sem_cache);
        }
        V(sem_count);  // count 访问完了，释放 
    }
}
```
把数据库看成共享区，然后按照读者写者方法管理线程访问数据库，即实现了读跟读并发，进一步提高速度。

但是业务层有读写顺序的要求，就是一定要按照业务层的调用顺序进行读写，用一个GCD的串行队列，保证读写顺序，遇到读的时候，开个新线程去并发执行，或者把读任务加入到并发队列中，遇到写的时候，就在串行队列中做。

5.小细节

比如，减少频繁创建和销毁对象。

频繁查询的字段缓存在内存中，并注意内存中与数据库中的数据的一致性问题

iOS 没有 Memcache 之类的框架，所以缓存状态得自己维护，还好移动端不是特别多要缓存的数据。

对比：

优化前：itouch 5, iOS 8 的环境，1000条写数据用时 18.7 秒
优化后：itouch 5, iOS 8 的环境，1000条写数据用时 370 ms
笔记本 SSD 环境，iOS虚拟机中，1000条写数据用时 18 ms
笔记本没装 sqlite，在虚拟机中测的，直接装物理机上估计会更快。