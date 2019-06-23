```mysql
create table student(
`id` int auto_increment,
`name` varchar(20) not null,
primary key(`id`)
)auto_increment=100000;//自增从100000开始

select last_insert_id();//返回最后一条插入数据的id
```

