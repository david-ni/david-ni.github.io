---
title: 基础Sql语句
description: 基础Sql语句
categories: 
- Database
- Mysql
---

## 表（Table）

### 创建表

>[MySQL CREATE TABLE Statement By Examples](http://www.mysqltutorial.org/mysql-create-table/)

- 语法

```sql
CREATE TABLE [IF NOT EXISTS] table_name(
    column_list
) ENGINE=storage_engine
```

1. `IF NOT EXISTS`是可选的，但推荐使用，它会先检查是否有有同名表，如果没有则创建。

2. `storage_engine` MySql 支持多种存储引擎: `MyISAM`、`InnoDB`、`MERGE`、`MEMORY (HEAP)`、`ARCHIVE`、`CSV`、`FEDERATED`，如果没有指定，默认值是`InnoDB`

在创建表的时候需要给定字段（或列名），格式如下：

```sql 
column_name data_type(length) [NOT NULL] [DEFAULT value] [AUTO_INCREMENT]
```
如果需要使用主键，可以使用以下语法：

```sql
PRIMARY KEY (col1,col2,...)
```

- 例子

```sql
CREATE TABLE IF NOT EXISTS tasks (
    task_id INT AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    start_date DATE,
    due_date DATE,
    status TINYINT NOT NULL,
    priority TINYINT NOT NULL,
    sex ENUM('FEMALE','MALE') NOT NULL DEFAULT 'MALE',
    description TEXT,
    PRIMARY KEY (task_id)
)  ENGINE=INNODB;
```

## 数据检索(SELECT)

- 语法

```sql
SELECT 
    [alias1.]column_1, [alias1.]column_2, ...
FROM
    table_1 [AS alias1],table_2 [AS alias2]
[INNER | LEFT |RIGHT] JOIN table_3 ON conditions
WHERE
    conditions
GROUP BY column_1
HAVING group_conditions
ORDER BY column_1
LIMIT offset, length;
```

### WHERE子句

>[MySQL WHERE](http://www.mysqltutorial.org/mysql-where/)

WHERE子句除了用在`SELECT`中还可以在`UPDATE`、`DELETE`中使用

#### 比较运算符

Operator | Description
---|---
= | 等于
<> or != | 不等于
< | 小于
\> | 大于
<= | 小于等于
>= | 大于等于

#### AND 操作符

>[MySQL AND Operator](http://www.mysqltutorial.org/mysql-and/)

- 语法

```
WHERE boolean_expression_1 AND boolean_expression_2
```

 AND  | TRUE  | FALSE | NULL
---|---|---|---
TRUE  | TRUE  | FALSE |	NULL
FALSE | FALSE |	FALSE |	FALSE
NULL  |	NULL  |	FALSE |	NULL

#### OR 操作符

>[MySQL OR Operator](http://www.mysqltutorial.org/mysql-or/)

```sql
boolean_expression_1 OR boolean_expression_2
```

 OR   | TRUE  | FALSE | NULL
---|---|---|---
TRUE  | TRUE  | TRUE  |	TRUE
FALSE | TRUE  |	FALSE |	NULL
NULL  |	TRUE  |	NULL  |	NULL

需要注意的是AND操作符的优先级大于OR操作符

#### BETWEEN

>[MySQL BETWEEN](http://www.mysqltutorial.org/mysql-between)

表示的是区间条件

- 语法
```sql
expr [NOT] BETWEEN begin_expr AND end_expr;
```
`expr`在/不在区间`begin_expr`和`end_expr`之间

`expr`、`begin_expr`和`end_expr`的数据类型必须相同

- 例子

```sql
SELECT 
    productCode, 
    productName, 
    buyPrice
FROM
    products
WHERE
    buyPrice BETWEEN 90 AND 100;
```

#### LIKE

> [MySQL LIKE](http://www.mysqltutorial.org/mysql-like/)

- 语法

```sql
expr LIKE 'pattern'
```
LIKE 支持以下通配符

1. 百分号%：匹配任何字符出现的任意次数（0次或多次）
2. 下划线_：匹配当个任意字符

#### IN

> [MySQL IN](http://www.mysqltutorial.org/sql-in.aspx)

- 语法

```sql
SELECT 
    column1,column2,...
FROM
    table_name1
WHERE 
 (expr|column_1) [NOT] IN ('value1','value2',...);
```
如果`column_1`或表达式的结果在集合中有匹配，将返回1否则返回0

- 例子

```sql
SELECT 
    officeCode, 
    city, 
    phone, 
    country
FROM
    offices
WHERE
    country IN ('USA' , 'France');
```

#### IS NULL

>[A Comprehensive Look at MySQL IS NULL Operator](http://www.mysqltutorial.org/mysql-is-null/)

- 语法

```sql
value IS [NOT] NULL
```
注意：NULL表示无值，它与0、空字符、false不同

### GROUP BY

>[](http://www.mysqltutorial.org/mysql-order-by/)

根据一个或多个列对结果集进行分组，通常而言，`GROUP BY` 会和`SUM`、`AVG`、`MAX`、`MIN`等函数使用

- 语法

```
SELECT 
    c1, c2,..., cn, aggregate_function(ci)
FROM
    table
WHERE
    where_conditions
GROUP BY c1 , c2,...,cn;
```
使用`GROUP BY`需要注意以下几点：

1. `GROUP BY`子句可以包含任意数目的列，因而可以对分组进行嵌套，更细致地进行数据分组。
2. `GROUP BY`必须在`FROM`和`WHREE`之后，`ORDER BY`之前
3. 一般情况下，`SELECT`中出现的非聚合函数字段，`GROUP BY`语句中也应该存在，比如
```sql
SELECT name, address, MAX(age) 
FROM t 
GROUP BY name;
```
运行此sql会报错：
```console
ERROR 1055 (42000): Expression #2 of SELECT list is not in GROUP
BY clause and contains nonaggregated column 'mydb.t.address' which
is not functionally dependent on columns in GROUP BY clause; this
is incompatible with sql_mode=only_full_group_by
```
为了修复这个错误，我们应该把`address`字段从`SELECT`中去掉或者在`GROUP BY`中添加`address`
```sql
SELECT name, address, MAX(age) 
FROM t 
GROUP BY name,address;
```
不过有一些特例，具体可以参考[MySQL Handling of GROUP BY](https://dev.mysql.com/doc/refman/8.0/en/group-by-handling.html)
4. 如果分组列中包含具有NULL值的行，则NULL将作为一个分组返回。如果列中有多行NULL值，它们将分为一组。
5. 如果在GROUP BY子句中嵌套了分组，数据将在最后指定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。 GROUP BY子句中列出的每一列都必须是检索列或有效的表达式（但不能是聚集函数）。如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式。不能使用别名。 

### HAVING

>[MySQL HAVING](http://www.mysqltutorial.org/mysql-having.aspx)

与`GROUP BY`配合使用，用于过滤分组

- 语法
```
HAVING condition
```
`HAVING`支持所有`WHERE`操作符(`AND`、`OR`、`IN`、`BETWEEN`、`LIKE`)

### ORDER BY

>[MySQL ORDER BY: Sort a Result Set](http://www.mysqltutorial.org/mysql-order-by/)

- 语法

```sql
SELECT column1, column2,...
FROM tbl
ORDER BY column1 [ASC|DESC], column2 [ASC|DESC],...
```
注意： 

1. 先按`column1`排序再按`column2`排序，这里只有当`column1`中的值相同时才按照`column2`排序
2. 如果未指定`ASC`或`DESC`，默认`ASC`

### LIMIT

>[MySQL LIMIT](http://www.mysqltutorial.org/mysql-limit.aspx)

- 语法

```sql
SELECT 
    column1,column2,...
FROM
    table
LIMIT offset , count;
```
`LIMIT`有两个参数：

1. `offset`: 指定开始的位置，从0开始
2. `count`: 代表要返回的行数

![image](/images/database/mysql-base-sql-limit.png)

### 子查询

>[MySQL Subquery](http://www.mysqltutorial.org/mysql-subquery/)

使用子查询的几种形式：

1. 在`WHERE`中使用

```sql
SELECT 
    lastName, firstName
FROM
    employees
WHERE
    officeCode IN (SELECT 
            officeCode
        FROM
            offices
        WHERE
            country = 'USA');
```

```sql
SELECT 
    customerNumber, checkNumber, amount
FROM
    payments
WHERE amount = (SELECT 
            MAX(amount)
        FROM
            payments);
```

2. 在`FROM`中使用

```sql
SELECT 
    MAX(items), MIN(items), FLOOR(AVG(items))
FROM
    (SELECT 
        orderNumber, COUNT(orderNumber) AS items
    FROM
        orderdetails
    GROUP BY orderNumber) AS lineitems;
```

### 联结（JOIN）

>[MySQL Join Made Easy For Beginners](http://www.mysqltutorial.org/mysql-join/)

在实际业务中，我们经常把数据分表存放，那如何把多个表里的数据用一个`SELECT`语句查询出来呢？这就需要联结。联结分成以下几种类型：Cross join、Inner join、Left join和Right join

下面通过一个例子来讲解它们之间的区别。

首先我们创建两个表`t1`和`t2`

```sql
CREATE TABLE t1 (
    id INT PRIMARY KEY,
    pattern VARCHAR(50) NOT NULL
);
 
CREATE TABLE t2 (
    id VARCHAR(50) PRIMARY KEY,
    pattern VARCHAR(50) NOT NULL
);
```

`t1`和`t2`表都有`pattern`列，现在我们插入一些数据

```sql
INSERT INTO t1(id, pattern)
VALUES(1,'Divot'),
      (2,'Brick'),
      (3,'Grid');
 
INSERT INTO t2(id, pattern)
VALUES('A','Brick'),
      ('B','Grid'),
      ('C','Diamond');
```

- **Cross join**

我们先来看一下Cross join的用法：
```sql
SELECT 
    t1.id, t2.id
FROM
    t1
CROSS JOIN t2;
```
运行结果如下：

![image](/images/database/mysql-base-sql-cross-join-result.png)

Cross join采用笛卡尔积的规则，实际上是将两个表相乘，得到一个组合表（3*3条数据）

![image](/images/database/mysql-base-sql-cross-join.png)

- **Inner join**

```sql
SELECT 
    t1.id, t2.id
FROM
    t1
        INNER JOIN
    t2 ON t1.pattern = t2.pattern;
```
Inner join 实际上是对Cross join的条件过滤，它将不满足`t1.pattern = t2.pattern`的纪录过滤掉：

![image](/images/database/mysql-base-sql-inner-join-result.png)

- **Left join**

获取左表所有记录，即使右表没有对应匹配的记录

```sql
SELECT 
    t1.id, t2.id
FROM
    t1
        LEFT JOIN
    t2 ON t1.pattern = t2.pattern
ORDER BY t1.id;
```

![image](/images/database/mysql-base-sql-left-join-result.png)

- **Right join**

与Left join相反，用于获取右表所有记录，即使左表没有对应匹配的记录。

```sql
SELECT 
    t1.id, t2.id
FROM
    t1
        RIGHT JOIN
    t2 on t1.pattern = t2.pattern
ORDER BY t2.id;
```

![image](/images/database/mysql-base-sql-right-join-result.png)

### 组合查询（UNION）

>[MySQL UNION](http://www.mysqltutorial.org/sql-union-mysql.aspx)

用于组合多个`SELECT`查询

- 语法

```sql
SELECT column_list
UNION [DISTINCT | ALL]
SELECT column_list
UNION [DISTINCT | ALL]
SELECT column_list
```
注意：

1. UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔（因此，如果组合四条SELECT语句，将要使用三个UNION关键字）。
2. UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过，各个列不需要以相同的次序列出）。
3. 列数据类型必须兼容：类型不必完全相同，但必须是可以隐含转换的类型（例如，不同的数值类型或不同的日期类型）。
4. ORDER BY子句排序。在用UNION组合查询时，只能使用一条ORDER BY子句，它必须位于最后一条SELECT语句之后。
5. UNION和JOIN的区别

![image](/images/database/mysql-base-sql-unin-join.png)

## 数据插入和更新

### INSERT

- 语法

```sql
// 插入单行数据
INSERT INTO table(c1,c2,...)
VALUES 
   (v11,v12,...);

// 插入多行数据
INSERT INTO table(c1,c2,...)
VALUES 
   (v11,v12,...),
   (v21,v22,...),
    ...
   (vnn,vn2,...);
```
注意：

1. 列和值需要一一对应

### UPDATE

>[MySQL UPDATE](http://www.mysqltutorial.org/mysql-update-data.aspx)

- 语法

```sql
UPDATE [LOW_PRIORITY] [IGNORE] table_name 
SET 
    column_name1 = expr1,
    column_name2 = expr2,
    ...
[WHERE
    condition];
```
UPDATE支持两种修饰符：

1. `LOW_PRIORITY`：延迟更新操作直到当前表没有读取操作，不过只有部分存储引擎支持该修饰符，比如：MyISAM, MERGE, MEMORY

2. `IGNORE`：允许Mysql在发生错误时继续更新操作

### DELETE

> [MySQL DELETE](http://www.mysqltutorial.org/mysql-delete-statement.aspx)

- 语法

```sql
DELETE FROM table_name
WHERE condition;
```
1. 如果`WHERE`条件没有指定，就会删除该表所有纪录



