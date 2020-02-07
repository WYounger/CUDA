##### 1.开启ssh服务

安装
sudo apt-get install openssh-server openssh-client

开启
service ssh start

查看22端口是否开启
lsof -i:22

##### 2.配置vmware虚拟网络(NAT模式)

查看虚拟机内系统的ip
配置端口映射:
ip:22 --> 127.0.0.1:任意IP2

##### 3.xshell连接

选择ssh协议
输入地址: 127.0.0.1:任意IP2
输入user/passowrd
连接