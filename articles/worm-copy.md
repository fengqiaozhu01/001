# 史上最简单的 MySQL 教程（十七）「数据的高级操作 之 蠕虫复制」

## 数据的高级操作

### 蠕虫复制

蠕虫复制：从已有的数据表中获取数据，然后将数据进行新增操作，数据成倍（以指数形式）的增加。

**根据已有表创建新表，即复制表结构**，其基本语法为：

 - `create table + 表名 + like + [数据库名.]表名;`

执行如下 SQL 语句，进行测试：

```
-- 根据已有表，创建新表，当两张表位于同一数据库时，可以省略数据库名称
create table my_copy like my_gbk;
```

![desc-mygbk](https://github.com/guobinhit/mysql-tutorial/blob/master/images/worm-copy/desc-mygbk.png)

如上图所示，表`my_copy`和表`my_gbk`的表结构完成相同。

蠕虫复制的步骤为：先查出数据，然后将查出的数据新增一遍。

 - **基本语法**：`insert into + 表名 + [()] + select + 字段列表/* + from + 表名;`

执行如上 SQL 语句，进行测试：

```
-- 蠕虫复制
insert into my_copy select * from my_collate_bin;
```

![select-mycollate](https://github.com/guobinhit/mysql-tutorial/blob/master/images/worm-copy/select-mycollate.png)

如上图所示，咱们已经成功将表`my_collate_bin`中的数据复制到表`my_copy`中啦！接下来，咱们再执行如下 SQL 语句，测试蠕虫复制的效果：

```
-- 蠕虫复制
insert into my_copy select * from my_copy;
```

![insert-mycopy](https://github.com/guobinhit/mysql-tutorial/blob/master/images/worm-copy/insert-mycopy.png)

如上图所示，通过观察每次执行 SQL 语句后影响的列数，分别为`4`、`8`和`16`等，咱们会发现蠕虫复制的效果呈（指数）爆炸性增长。

**蠕虫复制的意义**：

 1. 从已有的数据表中拷贝数据到新的数据表；
 2. 可以迅速的让表中的数据膨胀到一定的数量级，多用于测试表的压力及效率。

----------

**温馨提示**：符号`[]`括起来的内容，表示可选项；符号`+`，则表示连接的意思。


----------
———— ☆☆☆ —— [返回 -> 史上最简单的 MySQL 教程 <- 目录](https://github.com/guobinhit/mysql-tutorial/blob/master/README.md) —— ☆☆☆ ————
