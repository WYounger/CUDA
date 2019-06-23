#### 一.数据库基础

##### 1.基本规则

1. sql语句以分号结尾
2. 关键字不区分大小写
3. 字符串和日期常数需要使用单引号`''`括起来，数字常数无需家主单引号

##### 2.表的基本操作

```sql
-- 创建
CREATE TABLE <表名>
（<列名1> <数据类型> <该列所需约束>，
<列名2> <数据类型> <该列所需约束>，
<列名3> <数据类型> <该列所需约束>，
<列名4> <数据类型> <该列所需约束>，
<该表的约束1>， <该表的约束2>，……）

-- 删除
DROP TABLE <表名>

-- 修改
ALTER TABLE <表名> ADD COLUMN <列名 类型 约束> --oracle和SQL Server 不用谢COLUMN，下同
ALTER TABLE <表名> DROP COLUMN <列名>

-- 修改表名
ALTER TABLE <表名> RAENAME TO <新表名> --Oracle
sp_rename '<表名>','<新表名>' -- SQL Server
RENAME TABLE <表名> to <新表名>


```

#### 二.查询基础

##### 1.select语句基础

```sql
-- 列出部分列  查询结果中列的顺序和SELECT 子句中的顺序相同
SELECT <列名>，……
FROM <表名>；

-- 列出全部列
SELECT *
FROM <表名>;

-- 为列设定别名
SELECT product_id AS id,
product_name AS name,
purchase_price AS price
FROM Product;

-- 常数查询
-- 'string'是字符串常数,38是数字常数 '2009-02-24' 是日期常数
SELECT '商品' AS string, 38 AS number, '2009-02-24' AS date,
product_id, product_name
FROM Product;

-- DISTINCT只能在第一列之前
-- 从结果中删除重复行
SELECT DISTINCT product_type
FROM Product;

-- DISTINCT在多列之前使用。此时，会将多个列的数据进行组合，将重复的数据
-- 合并为一条
SELECT DISTINCT product_type, regist_date
FROM Product;

-- where 子句
SELECT <列名>, ……
FROM <表名>
WHERE <条件表达式>;
-- 执行顺序
-- 首先通过 WHERE 子句查询出符合指定条件的记录，然后再选取出 SELECT 语句指定的列

```

##### 2.算数运算符和比较运算符

1. 算数运算

   `+ - * /`

   ```sql
   SELECT product_name, sale_price,
   sale_price * 2 AS "sale_price_x2"
   FROM Product;
   ```

2. 所有包含 NULL 的计算，结果肯定是 NULL 

3. 比较运算符

   `=`     	相等
   `<>`   	不相等

   `>=` 		大于等于
   `>`		大于
   `<=` 		小于等于
   `< ` 		小于

4. NULL值的处理

   ```sql
   <列名> is null
   <列名> is not null
   ```

##### 3.逻辑运算符

1. 多个查询条件进行组合时，需要使用AND运算符或者OR运算符
2. 