---
title: Mysql 常用命令行
description: Mysql 常用命令行
categories: 
- Database
- Mysql
---

## 命令行

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

## 附录

### 作用范围`GLOBAL` 和 `SESSION`

在查看/设置Mysql系统变量时，需要指定作用范围`GLOBAL`或者`SESSION`。

- `GLOBAL`：全局变量，对所有客户端有效
- `SESSION`：会话变量，对当前客户端连接有效

举个例子，我们想修改一下默认的存储引擎为`MyISAM`，如果只想对当前会话生效，可以使用`SESSION`关键字：

```bash
SET SESSION default_storage_engine = MyISAM;
```

此时如果我们重新打开一个连接，使用`SHOW VARIABLES LIKE "default_storage_engine";`查看该变量信息发现`default_storage_engine`还是`InnoDB`，但如果使用`GLOBAL`关键字，则可以对所有新建的会话生效。

