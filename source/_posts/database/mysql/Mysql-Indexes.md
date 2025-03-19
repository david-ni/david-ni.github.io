在MySQL中，可以通过以下步骤为表建立索引：  

1. 选择需要建立索引的表和列。索引应该只建立在经常用于查询条件的列上，以提高查询性能。 

2.  使用CREATE INDEX语句创建索引。语法如下：  

   ```sql
   CREATE [UNIQUE] INDEX index_name ON table_name (column_name); 
   ```

    其中，`index_name`是索引的名称，`table_name`是表的名称，`column_name`是要建立索引的列的名称。  如果需要建立唯一索引，可以在`CREATE`语句中添加`UNIQUE`关键字。  

   例如，要在名为`users`的表的`email`列上建立一个唯一索引，可以使用以下语句： 

   ```sql
   CREATE UNIQUE INDEX idx_email ON users (email); 
   ```

   使用EXPLAIN语句检查查询以确认MySQL是否使用了索引。语法如下：  EXPLAIN SELECT statement;  其中，SELECT statement是要执行的查询语句。  如果MySQL使用了索引，则会在结果中显示使用了索引的信息。  以下是一个示例代码，用于在MySQL中为表建立索引：

   ```sql
   -- 创建名为users的表
   CREATE TABLE users (
     id INT PRIMARY KEY,
     name VARCHAR(50),
     email VARCHAR(50)
   );
   
   -- 在email列上建立唯一索引
   CREATE UNIQUE INDEX idx_email ON users (email);
   
   -- 检查查询是否使用了索引
   EXPLAIN SELECT * FROM users WHERE email = 'example@example.com';
   
   ```

3. MySQL中的EXPLAIN命令用于解析查询语句，以帮助我们了解MySQL如何执行查询。EXPLAIN返回的结果集包含以下几列信息：  1. id：每个表的唯一标识符，用于标识表的连接顺序。

4. 2. table：表的名称。
   3.  3. type：MySQL使用哪种访问类型来读取表的数据。例如，ALL表示MySQL将扫描整个表，INDEX表示MySQL将使用索引扫描表，PRIMARY表示MySQL将使用主键来读取表的数据。 4. possible_keys：MySQL可以使用的索引列表。 5. key：MySQL实际使用的索引。如果没有使用索引，则为NULL。 6. key_len：使用的索引长度。 7. ref：MySQL用于查找行的列或常量的列表。如果没有ref，则为NULL。 8. rows：MySQL估计的从表中返回的行数。 9. Extra：其他额外的信息，例如“Using where”表示MySQL将应用WHERE子句。