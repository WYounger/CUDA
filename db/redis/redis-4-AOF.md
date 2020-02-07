##### aof运行原理

<div align="center"><img src="images\aof运行原理-创建.png"></div>

<div align="center"><img src="images\aof运行原理-恢复.png"></div>

##### aof三种策略

**always**

<div align="center"><img src="images\aof-always.png"></div>

**everysec**

<div align="center"><img src="images\aof-everysec.png"></div>

**no**

<div align="center"><img src="images\aof-no.png"></div>

##### 比较

<div align="center"><img src="images/always-everysec-no.jpg"></div>

##### AOF

aof重写举例



<div align="center"><img src="images/aof重写实例.png"></div>

**实际是在内存中进行重写，然后写入aof文件**

减少磁盘占用量

加速恢复速度

aof重写两种实现方式

bgrewriteaof

<div align="center"><img src="images/bgrewriteaof命令.png"></div>

aof重写配置

<div align="center"><img src="images/aof重写配置.png"></div>

aof重写流程

<div align="center"><img src="images/aof重写流程.png"></div>

aof相关配置

<div align="center"><img src="images/aof配置.png"></div>



rdb与aof比较

<div align="center"><img src="images/rdb-aof.png"></div>

