# SQLite3中自增主键归零方法

当SQLite数据库中包含自增列时，会自动建立一个名为 sqlite_sequence 的表

这个表包含两个列：name和seq。name记录自增列所在的表，seq记录当前序号（下一条记录的编号就是当前序号加1）

如果想把某个自增列的序号归零，只需要修改 sqlite_sequence表就可以了
```
UPDATE sqlite_sequence SET seq = 0 WHERE name='TableName';
```

也可以直接把该记录删掉： 
```
DELETE FROM sqlite_sequence WHERE name='TableName';
```

要想将所有表的自增列都归零，直接清空sqlite_sequence表就可以了：
```
DELETE FROM sqlite_sequence;
```