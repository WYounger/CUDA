#### 1.更换yum源

```shell
# 1.备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 2.下载aliyun.repo
# 注意版本
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# 3.生成缓存
yum makecache
```

#### 2.ll命令

```shell
# ll 命令是ls-l的别名
# 当遇到没有ll命令时，可以检查 ~/.bashrc是否配置有ll别名
# ~/.bashrc：
# .bashrc
  
# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias ll='ls -l' #无，则添加
# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

#添加后,是该文件生效
source ~/.bashrc
```

#### 3.命令和软件的安装

查看命令所属包

```shell
yum provides cmd

# eg:查看clear命令来自哪个包
yum provides clear
#输出如下
Last metadata expiration check: 1:10:50 ago on Sat Jan 18 03:49:26 2020.
ncurses-6.1-7.20180224.el8.x86_64 : Ncurses support utilities
Repo        : @System
Matched from:
Filename    : /usr/bin/clear
#ncurses-6.1-7.20180224.el8.x86_64 : Ncurses support utilities
#当前到了这个版本，直接运行yun install ncurses就可安装clear，不用指定包的版本号
```

安装软件

```shell
yum install package
```

