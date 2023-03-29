---
title: Mysql 开发笔记
description: 记录Mysql使用过程中的问题
categories: 
- Database
- Mysql

---

## Mysql常用命令

```bash
# 查看帮助 
help/mysql --help;

# 查看版本 
mysql --version;

# 登录
mysql -u[userName] -p[password] -D database;

# 显示服务器支持的引擎
SHOW ENGINES;

## 查看mysql系统变量
SHOW [GLOBAL|SESSION] VARIABLES [LIKE 匹配的模式];

## 设置系统变量
SET [GLOBAL|SESSION] 系统变量名 = 值;

## 查看状态变量
SHOW [GLOBAL|SESSION] STATUS [LIKE 匹配的模式];

# 显示数据库列表
SHOW DATABASES;

# 显示当前已打开的数据库
SELECT DATABASE();

# 切换数据库
USE databasename;

# 显示当前数据库的表
SHOW TABLES;

# 显示tablename这个表的详细信息 
DESCRIBE tablename;

```

>作用范围`GLOBAL` 和 `SESSION`

在查看/设置Mysql系统变量时，需要指定作用范围`GLOBAL`或者`SESSION`。

- `GLOBAL`：全局变量，对所有客户端有效
- `SESSION`：会话变量，对当前客户端连接有效

举个例子，我们想修改一下默认的存储引擎为`MyISAM`，如果只想对当前会话生效，可以使用`SESSION`关键字：

```bash
SET SESSION default_storage_engine = MyISAM;
```

此时如果我们重新打开一个连接，使用`SHOW VARIABLES LIKE "default_storage_engine";`查看该变量信息发现`default_storage_engine`还是`InnoDB`，但如果使用`GLOBAL`关键字，则可以对所有新建的会话生效。

## 什么时候我们需要使用反引号（``）和引号

> [Where do we use backticks and quotes in SQL?](https://stackoverflow.com/questions/42319049/where-do-we-use-backticks-and-quotes-in-sql)

反引号（``）一般用在数据库名、表名和列名中，为了区分MYSQL的保留字与普通字符而引入的符号。

```sql
SELECT `select` FROM `test` WHERE `select`='字段值'
```

只有在`sql`语句与保留字冲突时才需要
使用反引号（``）。

引号一般用在字段的值,如果字段值是字符或字符串，则要加引号

```sql
SELECT * FROM mydb.users WHERE username = "jim";
```

##  查看mysql默认读取配置文件

如果没有设置使用指定目录的my.cnf，mysql启动时会读取安装目录根目录及默认目录下的my.cnf文件。

查看mysql启动时读取配置文件的默认目录。

```bash
mysql --help | grep 'my.cnf'
```
输出

```bash
order of preference, my.cnf,$MYSQL_TCP_PORT,

/etc/my.cnf /etc/mysql/my.cnf /usr/local/etc/my.cnf ~/.my.cnf
```
`/etc/my.cnf`, `/etc/mysql/my.cnf`, `/usr/local/etc/my.cnf`, `~/.my.cnf` 这些就是mysql默认会搜寻my.cnf的目录，顺序排前的优先。 

## mysql中的utf8和utfmb4

> [记住，永远不要在 MySQL 中使用 “utf8” 编码](https://learnku.com/articles/13864/remember-never-use-utf8-encoding-in-mysql)

> [How to support full Unicode in MySQL databases](https://mathiasbynens.be/notes/mysql-utf8mb4)

> [What's the difference between utf8_general_ci and utf8_unicode_ci?](https://stackoverflow.com/questions/766809/whats-the-difference-between-utf8-general-ci-and-utf8-unicode-ci)

mysql支持多种字符集，可以使用`SHOW CHARACTER SET;`来获取所有支持的字符集。

一般情况下，我们更偏爱`utf8`作为默认字符集，我们知道`utf8`是可伸缩最大支持4字节的字符编码，但是由于历史原因，在mysql中`utf8`最大只能支持3字节，这就会导致一些字符丢失，所以使用`utfmb4`才是正确的选择。

### 字符集相关命令

```bash 
# 获取所有支持的字符集
# 在连接mysql后使用
SHOW CHARACTER SET;

# 获取字符集相关变量
SHOW VARIABLES LIKE "character%";
```

## mysql中的`Collation`

> [MYSQL中的COLLATE是什么？](https://juejin.cn/post/6844903726499512334)

![mysql-collation](https://user-images.githubusercontent.com/7795335/100986206-da0bde00-3587-11eb-8282-9c0fecdda363.jpg)

简而言之，`Collation`是用于字段之间比较排序的，会影响到`ORDER BY`语句的顺序，会影响到`WHERE`条件中大于小于号筛选出来的结果，会影响`**DISTINCT**`、`**GROUP BY**`、`**HAVING**`语句的查询结果。

在mysql中使用`show collation`指令来查看所有的`collation`，通常情况下`collation`都是与字符集相关的