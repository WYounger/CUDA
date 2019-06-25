##### 1.主键自增

```mysql
create table student(
`id` int auto_increment,
`name` varchar(20) not null,
primary key(`id`)
)auto_increment=100000;//自增从100000开始

select last_insert_id();//返回最后一条插入数据的id
```

##### 2.limit

```sql
SELECT 
    column_1, column_2, ...
FROM
    table_1
[INNER | LEFT |RIGHT] JOIN table_2 ON conditions
WHERE
    conditions
GROUP BY column_1
HAVING group_conditions
ORDER BY column_1
LIMIT offset, length; 
-- 摘自: https://www.yiibai.com/mysql/select-statement-query-data.html

-- offset: 0 1 2 3 ... n
-- row   : 1 2 3 4 ... n+1

-- 执行顺序
-- from-where-group by-having-select-order by-limit

-- limit通常和order by配合使用，返回前几名记录

-- eg:
select * from `goods` limit 0,3;--等价于
select * from `goods` limit 3;

select * from `goods` order by `price` DESC limit 1,3; -- 返回第二名到第五名的记录

-- 参考连接: https://www.yiibai.com/mysql/limit.html
```

