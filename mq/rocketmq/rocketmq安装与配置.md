##### 1.安装

```sh
wget https://mirrors.tuna.tsinghua.edu.cn/apache/rocketmq/4.6.0/rocketmq-all-4.6.0-bin-release.zip


#新建一个新的程序文件夹用来放rocketmq
mkdir -p /usr/local/rocketmq
解压：
unzip rocketmq-all-4.6.0-bin-release.zip -d /usr/local/rocketmq

# 进入目录
cd /usr/local/rocketmq/
mkdir logs
 
# 1.先启动mqnamesrv  
#启动命令并加上输出日志
nohup sh bin/mqnamesrv  >  ./logs/namesrv.log  &
tail -f ./logs/namesrv.log
# 停止命令
nohup sh bin/mqshutdown namesrv
 
# 2.启动broker

#注意，下面的ip地址以及端口请填写你安装的rocketmq机器的ip地址及端口。
nohup sh bin/mqbroker -c conf/broker.conf  -n xxx.xx.xx.xx:9876  >  ./logs/broker.log &
tail  -f  ./logsbroker.log
# 关闭命令
nohup sh bin/mqshutdown broker

################ 当涉及到内网和外网时，需要按照以下配置 ##################
#在broker.conf末尾添加 brokerIP1=外网IP,然后再启动namesrv和broker
nohup sh bin/mqnamesrv -n 外网IP:9876 &
nohup sh bin/mqbroker -c conf/broker.conf  -n 外网IP:9876 autoCreateTopicEnable=true &
```

##### 2.控制台搭建

1. 控制台源码

   https://github.com/apache/rocketmq-externals,下载	`rocketmq-console`

2. 修改`application.properties`

   ```properties
   #配置console的运行端口
   server.port=8080
   #配置name server ip
   rocketmq.config.namesrvAddr=47.105.109.48:9876
   ```

3. 打包上传服务器

   ```java
   //maven命令打包
   mvn clean package -Dmaven.test.skip=true
   //或，将项目导入ieda打包
   ```

   打包好后，然后将`jar`包上传服务器

4. 运行`jar`包

   ```sh
   java -jar rocketmq-console-ng-1.0.1.jar & # '&':后台启动
   ```

   