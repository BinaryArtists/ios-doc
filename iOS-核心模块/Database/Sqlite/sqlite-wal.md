# SQLite WAL与回滚日志

WAL全名：WAL(Write-Ahead Log)模式。它是另一种实现事务原子性的方法。（高并发）



*优点*

* 在大多数情况下更快
* 并行性更高。因为读操作和写操作可以并行。
* 文件IO更加有序化，串行化（more sequential）
* 使用fsync()的次数更少，在fsync()调用时好时坏的机器上较为未定。


*缺点*

* 一般情况下需要VFS支持共享内存模式。(shared-memory primitives)
* 操作数据库文件的进程必须在同一台主机上，不能用在网络操作系统。
* 持有多个数据库文件的数据库连接对于单个数据库时原子的，对于全部数据库是不原子的。
* 进入WAL模式以后不能修改page的size。
* 不能打开只读的WAL数据库(Read-Only Databases)，这进程必须有"-shm"文件的写权限。
* 对于只进行读操作，很少进行写操作的数据库，要慢那么1到2个百分点。
* 会有多余的"-wal"和"-shm"文件
* 需要开发者注意checkpointing

## 回滚日志

回滚日志的方法是把为改变的数据库文件内容写入日志里，然后把改变后的内容直接写到数据库文件中去。

在系统crash或掉电的情况下，日志里的内容被重新写入数据库文件中。日志文件被删除，标志commit这一次commit的结束。

## WAL模式


## 参考：

1. [Sqlite学习笔记(一)&&编译安装](http://www.cnblogs.com/cchust/p/4722931.html)
2. [Sqlite学习笔记(二)&&性能测试](http://www.cnblogs.com/cchust/p/4738002.html)
3. [Sqlite学习笔记(三)&&WAL性能测试](http://www.cnblogs.com/cchust/p/4754580.html)
4. [Sqlite学习笔记(四)&&SQLite-WAL原理](http://www.cnblogs.com/cchust/p/4754619.html)

