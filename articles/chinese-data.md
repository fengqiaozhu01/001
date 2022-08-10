# 史上最简单的 MySQL 教程（五）「中文数据问题」

中文数据问题
======

**中文数据问题的本质就是字符集的问题。**

由于计算机仅识别二进制数据，而且人类则更倾向于识别字符（符号），因此就需要一个二进制与字符的对应关系，也就是**字符集**。

在咱们通过 MySQL 数据库的客户端向服务器插入中文数据的时候，有可能失败，原因则可能是客户端和服务器的字符集设置不同导致的，例如：

 - 客户端的字符集为`gbk`，则一个中文字符，对应两个字节；
 - 服务器的字符集为`utf8`，则一个中文字符，对应三个字节。

这样显然会在编码转换的过程中出现问题，从而导致插入中文数据失败。

由于所有的数据库服务器表现的一些特性都是通过服务器端的变量来保持的，因此系统会先读取自己的变量，看看具体的表现形式。这样的话，咱们就可以通过以下语句查看服务器到底识别哪些字符集：

```
-- 查看服务器识别的全部字符集
show character set;
```

![show-character-set](https://github.com/guobinhit/mysql-tutorial/blob/master/images/chinese-data/show-character-set.png)

通过以上查询，咱们会发现：**服务器是万能的，其支持所有字符集。**

既然服务器支持这么多字符集，总会有一种是服务器默认的和客户端打交道的字符集。因此，咱们可以通过以下语句查看服务器默认的对外处理的字符集：

```
-- 查看服务器默认的对外处理的字符集
show variables like 'character_set%'; 
```
![show-variables](https://github.com/guobinhit/mysql-tutorial/blob/master/images/chinese-data/show-variables.png)

 - 标注1：服务器默认的客户端传来的数据字符集为`utf8`；
 - 标注2：连接层字符集为`utf8`；
 - 标注3：当前数据库的字符集为`utf8`；
 - 标注4：服务器默认的对外处理的字符集`utf8`.

通过以上查询，咱们会发现：**服务器默认的对外处理的字符集是`utf8`**.

那么反过来，咱们在通过客户端的属性查看客户端支持的字符集：

![mysql-command-line-client](https://github.com/guobinhit/mysql-tutorial/blob/master/images/chinese-data/mysql-command-line-client.png)

显然，咱们已经找到了问题的根源，确实是：**客户端支持的字符集为`gbk`，而服务器默认的对外处理的字符集为`utf8`，因此产生矛盾。**

既然问题已经找到了，那么解决方案就是：**修改服务器默认接收的字符集为`gbk`**.

```
-- 修改服务器默认接收的字符集为 GBK（不区分大小写）
set character_set_client = gbk;
```

这样的话，咱们再插入中文数据的时候，就会插入成功啦！But，在咱们查看数据的时候，又发现了一个问题，就是之前咱们插入的中文数据显示乱码啦！不过这也正常，因为查询的时候，数据的来源是服务器（`utf8`），解析数据的是客户端，而客户端仅识别`gbk`格式的数据，显示乱码也就在意料之中啦！

因此，解决方案就是：**修改服务器给客户端的数据字符集为`gbk`**.

```
-- 修改服务器给客户端的数据字符集为 GBK（不区分大小写）
set character_set_results = gbk;
```

![select-from-student](https://github.com/guobinhit/mysql-tutorial/blob/master/images/chinese-data/select-from-student.png)

如上图所示，向服务器插入中文数据的问题已经解决啦！

----------
此外，咱们之前使用的 SQL 语句：

```
-- 修改的只是会话级别，即当前客户端当次连接有效，关闭后失效
set 变量 = 值;
```
这样的话，每当咱们重启客户端的时候，都要依次重新进行设置，比较麻烦，因此咱们可以使用快捷的设置方式，即：

```
set names 字符集;
```

例如，

```
/**
* 恒等于 set character_set_client = gbk;
* 恒等于 set character_set_results = gbk;
* 恒等于 set character_set_connection = gbk;
*/
set names gbk;
```

表示上述一条语句，将同时改变三个变量的值。其中，`connection`为连接层，是字符集转换的中间者，如果其和`client`和`results`的字符集一致，则效率更高，不一致也没有关系。


----------
———— ☆☆☆ —— [返回 -> 史上最简单的 MySQL 教程 <- 目录](https://github.com/guobinhit/mysql-tutorial/blob/master/README.md) —— ☆☆☆ ————
