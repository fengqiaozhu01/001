# 史上最简单的 MySQL 教程（十六）「数据的高级操作 之 主键冲突」

## 数据的高级操作

数据的操作，无外乎**增删改查**。

新增数据的基本语法为：

 - `insert into + 表名 + [(字段列表)] + values (值列表);`

在数据插入的时候，假设主键对应的值已经存在，则插入失败！这就是主键冲突。

### 主键冲突

当主键存在冲突（duplicate key）的时候，可以选择性的进行处理，即更新或替换。

下面，以表`my_class`为例，进行测试：

![desc-myclass](https://github.com/guobinhit/mysql-tutorial/blob/master/images/duplicate-primary-key/desc-myclass.png)

其中，`grade`为主键。

**第一种情况：主键冲突，进行更新操作。**

 - 基本语法：`insert into + 表名 + [(字段列表：包含主键)] + values (值列表) on duplicate key update 字段 = 新值;`

执行如下 SQL 语句，进行测试：

```
-- 测试主键冲突的 SQL 语句
insert into my_class values ('PM3527','B315');
```

![insert-myclass](https://github.com/guobinhit/mysql-tutorial/blob/master/images/duplicate-primary-key/insert-myclass.png)

如上图所示，当主键已经存在的时候，产生主键冲突。再执行如下 SQL 语言，解决主键冲突的问题：

```
-- 当主键冲突的时候，进行更新操作
insert into my_class values ('PM3527','B315')
-- 冲突处理
on duplicate key update
-- 更新主键值
room = 'B315';
```

![select-myclass](https://github.com/guobinhit/mysql-tutorial/blob/master/images/duplicate-primary-key/select-myclass.png)

**第二种情况：主键冲突，选择替换操作。**

 - 基本语法：`replace insert into + 表名 + [(字段列表：包含主键)] + values (值列表);`

执行如下 SQL 语句，进行测试：

```
-- 测试主键冲突的 SQL 语句
insert into my_class values ('PM3528','B215');
```

![insert-error](https://github.com/guobinhit/mysql-tutorial/blob/master/images/duplicate-primary-key/insert-error.png)

如上图所示，当主键已经存在的时候，产生主键冲突。再执行如下 SQL 语言，解决主键冲突的问题：

```
-- 当主键冲突的时候，进行替换操作
replace into my_class values ('PM3528','B215');
```

![replace-myclass](https://github.com/guobinhit/mysql-tutorial/blob/master/images/duplicate-primary-key/replace-myclass.png)

通过以上两种情况的演示，当再发生主键冲突的时候，咱们已经可以从容应对并解决啦！


----------

**温馨提示**：符号`[]`括起来的内容，表示可选项；符号`+`，则表示连接的意思。


----------
———— ☆☆☆ —— [返回 -> 史上最简单的 MySQL 教程 <- 目录](https://github.com/guobinhit/mysql-tutorial/blob/master/README.md) —— ☆☆☆ ————
