#### 1.redis安装和使用

##### 1.安装

```bash
$ wget http://download.redis.io/releases/redis-5.0.5.tar.gz #下载
$ tar -zxvf redis-5.0.5.tar.gz #解压
$ cd redis-5.0.5
$ make #编译

#编译之后生成redis服务程序redis-server和redis客户程序redis-cli

#启动redis服务
#第一种方式
$ cd src
$ ./redis-server #启用默认设置
#第二种方式
$ cd src
$ ./redis-server redis.conf #启用自定义配置

#启动redis客户应用
$ cd src
$ ./redis-cli
#退出
$ shutdown
$ exit



#############################################################
$ wget http://download.redis.io/releases/redis-5.0.5.tar.gz #下载
$ tar -zxvf redis-5.0.5.tar.gz #解压
$ cd redis-5.0.5
$ make #编译
$ make install #安装==>可以在任意目录使用redis-server redis-cli命令

$ redis-server redis.conf #使用配置文件启动服务
$ redis-cli -h ip -p port #连接服务


 
```

##### 2.使用

ubuntu安装在vmvare上，网络采用NAT模式，ubuntu我是可以访问访问外网，但外网是不能访问ubuntu上的服务的，所以要设置访问windows下ip+端口转换为ubuntu下ip+端口，从而访问到ubuntu下的服务.

具体步骤如下:

在vmvare上,编辑-->虚拟网络编辑器-->更改设置-->选中NAT模式-->NAT设置

-->添加-->`主机端口` `类型` `虚拟机IP地址`+`虚拟机端口`

`主机端口`:NAT转发端口，外界通过访问主机的该端口，然后转发到虚拟机上

`类型`:TCP

`虚拟机IP地址`:Ubuntu的IP地址(通过`ifconfig`查看)

`虚拟机端口`:Ubuntu上服务的端口

然后开启redis远程服务

```bash
#修改redis.conf，然后以该文件为启动参数
bind 127.0.0.1 #注释掉
protected-mode yes #改为no

#关闭防火墙
#...
```

远程访问

```java
//主机ip：若为本地访问可以为127.0.0.1或者localhost 
//port:NAT转发端口
Jedis jedis = new Jedis("主机ip",6379);
System.out.println(jedis.ping());//打印出pong，表明连接成功
```

#### 2.基本配置

##### 1.日志配置

```bash
#日志等级
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel debug

#日志路径
logfile "/home/young/redis-5.0.5/log/log-6379.log" #要先创建好
```

##### 2.rdb

```bash
################################ SNAPSHOTTING  ################################
#
# Save the DB on disk:
#
#   save <seconds> <changes>
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#
#   save ""
#注释掉自动写入
# save 900 1
# save 300 10
# save 60 10000

# By default Redis will stop accepting writes if RDB snapshots are enabled
# (at least one save point) and the latest background save failed.
# This will make the user aware (in a hard way) that data is not persisting
# on disk properly, otherwise chances are that no one will notice and some
# disaster will happen.
#
# If the background saving process will start working again Redis will
# automatically allow writes again.
#
# However if you have setup your proper monitoring of the Redis server
# and persistence, you may want to disable this feature so that Redis will
# continue to work as usual even if there are problems with disk,
# permissions, and so forth.
stop-writes-on-bgsave-error yes #当执行bgsave发生异常，停止写入

# Compress string objects using LZF when dump .rdb databases?
# For default that's set to 'yes' as it's almost always a win.
# If you want to save some CPU in the saving child set it to 'no' but
# the dataset will likely be bigger if you have compressible values or keys.
rdbcompression yes #压缩数据后保存

# Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
# This makes the format more resistant to corruption but there is a performance
# hit to pay (around 10%) when saving and loading RDB files, so you can disable it
# for maximum performances.
#
# RDB files created with checksum disabled have a checksum of zero that will
# tell the loading code to skip the check.
rdbchecksum yes #检查和

# The filename where to dump the DB
dbfilename dump-6379.rdb #指定备份文件名

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
dir /home/young/redis-5.0.5/data/ #指定工作目录 
#默认 ./ 表示运行redis-server时的所在目录（工作目录）

```

