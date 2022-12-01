---
title: Mybatis 开发笔记
description: Mybatis使用过程笔记
categories: 
- Database
- Mybatis
---

## 前言

> Mybatis使用过程笔记

## Mybatis返回`Insert`后的主键`id`值

```xml
<insert id="createActivity" parameterType="Activity" keyColumn="id" keyProperty="id" useGeneratedKeys="true">
    INSERT INTO activity....
</insert>
```

这里需要指定几个属性:

- **useGeneratedKeys**: `(insert and update only) This tells MyBatis to use the JDBC getGeneratedKeys method to retrieve keys generated internally by the database (e.g. auto increment fields in RDBMS like MySQL or SQL Server). Default: false`，即告知Mybatis取出数据库生成的主键值

- **keyProperty**: `(insert and update only) Identifies a property into which MyBatis will set the key value returned by getGeneratedKeys, or by a selectKey child element of the insert statement. Default: unset. Can be a comma separated list of property names if multiple generated columns are expected.`即指定类中对应要赋值的属性名称，比如`Activity`中的`id`属性

- **keyColumn**: `(insert and update only) Sets the name of the column in the table with a generated key. This is only required in certain databases (like PostgreSQL) when the key column is not the first column in the table. Can be a comma separated list of columns names if multiple generated columns are expected.`

## Mybatis 处理集合

在开发过程中，经常会遇到“一对多“的关系，举个例子:

```java
@Data
public class Activity{
    private Long id;
    private String name;
    private List<GroupItem> groups;
   // ....
}
```
```sql
SELECT ac.*,g.name as group_name,g.id as group_id
FROM activity AS ac
LEFT JOIN activity_group AS ag ON ac.id = ag.activity_id
LEFT JOIN groups AS g ON ag.group_id = g.id AND g.status = 0
WHERE ac.id = #{activityId} AND ac.status = 0
```
一个活动(`Activity`)对应多个分组(`group`)，这种情况下可以Mybatis中的`collection `：

```xml
<resultMap id="ActivityResultMap" type="Activity">
        <result column="id" property="id" jdbcType="BIGINT"/>
        <result column="name" jdbcType="VARCHAR" property="name"/>
        <collection property="groups" ofType="GroupItem" javaType="java.util.List">
            <result property="id" column="group_id"/>
            <result property="name" column="group_name"/>
        </collection>
    </resultMap>
```