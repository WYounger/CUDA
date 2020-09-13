##### 事务

redis事务是一系列redis操作。`multi`开启事务，然后命令依次入队，最后`exec`执行队列中的命令

```bash
multi #开启事务
执行命令
exec/discard #执行事务/放弃事务
```

```bash
watch obj
multi
执行命令
exec
unwatch
# 执行exec时，如果我们的监视的对象被另一个连接操作改变了，那么事务执行失败
```





##### 慢查询

1. 慢查询生命周期

<div align="center"><img src="images/慢查询-生命周期.png"></div>



2. 两个配置

```bash
config set slowlog-max-len 1000 #设置队列长度
config set slowlog-log-slower-than 1000 #命令执行时间大于该值，则进入等待队列
```

3. 慢查询命令

```bash
slowlog get [n] #获取慢查询队列
slowlog len #获取慢查询队列长度
```

##### pipeline



<div align="center"><img src="images/批量通信网络模型.png"></div>



<div align="center"><img src="images/流水线.png"></div>

