#### 1.环境准备(win10)

1. 下载zip包(推荐从清华镜像下载)并解压

2. 配置环境变量: mysql/bin 加入path

3. 新建my.ini配置文件

   修改路径: basedir datadir

   ```ini
   [mysqld]
   # 设置3306端口
   port=3306
   # 设置mysql的安装目录
   basedir=C:\MyFile\Software\mysql-8.0.17-winx64
   # 设置mysql数据库的数据的存放目录
   datadir=C:\MyFile\Software\mysql-8.0.17-winx64\data
   # 允许最大连接数
   max_connections=200
   # 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
   max_connect_errors=10
   # 服务端使用的字符集默认为UTF8
   character-set-server=utf8
   # 创建新表时将使用的默认存储引擎
   default-storage-engine=INNODB
   # 默认使用“mysql_native_password”插件认证
   default_authentication_plugin=mysql_native_password
   [mysql]
   # 设置mysql客户端默认字符集
   default-character-set=utf8
   [client]
   # 设置mysql客户端连接服务端时默认使用的端口
   port=3306
   default-character-set=utf8
   ```

4. 安装与初始化

   ```bash
   # cmd,admin执行
   
   #安装
   mysqld install
   #初始化
   mysql --initialize --console
   #产生密码: root@localhost: password
   ```

5. 连接

   ```bash
   # 启动
   net start mysql #cmd,admin执行
   #连接
   mysql -uroot -ppwd
   
   #首次使用，在执行前必须使用alter user修改密码，否则报错
   alter user user() identified by "newPwd";
   
   #关闭
   net stop mysql #cmd,admin执行
   ```


#### 2.基本命令

##### 1.启动和连接

```shell
net start mysql;
net stop mysql;

#连接
mysql [ -h ip [ -P port ] ] -u userName -p pwd #默认连接本地、3306端
```

##### 2.用户管理

```bash
#修改密码
alter user user() identtified by 'newPwd' #修改当前用户密码
alter user 用户名[@'登陆主机'] identified by 'newPwd' #修改用户userName的密码

#新增用户
create user 用户名[@'登陆主机'] identified by 'pwd';

#授权
grant 权限 on dbName.* to 用户名[@'登陆主机'];
# 权限: insert,delete,update,select,drop 多个用','隔开 所有权限: all privileges
# *: 任意库名或者任意表名
# 登陆主机: 远程登陆主机IP %: 所有IP
flush privileges;

# 删除用户
 drop user 用户名[@'登陆主机'];
 
 #[@'用户主机']可以省略，默认为 localhost
```

##### 3.数据库操作

```shell
# 显示素有数据库
show databases;
# 创建数据库
create database 数据库名;
# 切换数据库
use 数据库名;
#　显示当前数据库
select database();
# 显示所有表
show tables;
# 删除数据库
drop database 数据库名;

# 创建表
create table <表名>  (<字段名 1> <类型 1> [,..<字段名 n> <类型 n>]);
# 查看表结构
desc 表名;
# 删除表
drop table 表名;
# 修改表名
rename table 原表名 to 新表名;
```

#### 3.参考

[MySQL常用命令总结](<https://www.cnblogs.com/qmfsun/p/4845837.html>)



