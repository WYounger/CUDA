#### 1.命令行模式下命令的执行

1. ##### 开始执行命令

```bash
command [-options] parameter1 parameter2
# 不论shell有多少个空格，只会视为一个
# 在linux系统中，区分字母大小写
```

##### 2.基础命令的操作

```bash
date [+%Y-%m-%d]#格式化输出
cal  [month] [year] #日历
bc   #简单计算器
```

3. ##### 热键

`[Tab]` 命令补全、参数补全、文件名补全

`[Ctrl]-c` 终止当前运行的程序

`[Ctrl]-d` 键盘输入结束，退出命令模式，退出root用户模式

4. 查看帮助信息

```bash
command --help
man command
```

#### 2.用户和用户组

文件拥有者

用户组

其他人:用户组外的人

#### 3.文件权限和属性

##### 1.`ls -al`

`ls`是`list`，显示文件的文件名和属性，`-al`列出权限和属性，包括隐藏的文件

##### 2.文件的详细信息

```bash
-rwxr-xr-- 1 young young 3771 5月  25 17:19 .bashrc
#文件类型
# 用户/组/其他人的权限
#        链接次数
#            用户名 组名 文件大小(byte)
#                              创建时间或最后修改的时间
#                                           文件名
```

​       **第一个字符代表这个文件的类型**

​      `[d] ` : 目录

​      `[-]`:文件

​      `[l]`:链接文件

​      `[b]`:存储设备

​      `[c]`:串行端口设备，例如键盘、鼠标

​       **[rwx]**

​       `[r]`可读  `[w]`可写  `[x]`可执行

​        注意这三个权限的位置不会改变，如果没有权限用`[-]`代替

##### 3.修改属性和权限

1. 修改用户组`chgrp`

       ```bash
chgrp groupName [-R] dirName/fileName #-R递归修改
       ```

2. 修改拥有者`chown`

```bash
chown owner [-R] firName/fileName #-R递归修改
```

3. 修改权限

   数字类型修改文件权限

   r:4 w:2 x:1

   ```bash
chmod 777 [-R] dirName/fileName #-R递归修改
   ```

   符号类型修改文件权限

| chmod | u g o a | + -  = | r w x | 文件或目录 |
| ----- | ------- | ------ | ----- | ---------- |

​      u:user g:group o:others a:all

​     +:添加 -:删除 =：设置

```bash
chmod u=rwx,go=rx fileName #rwxr-xr-x
chmod a+w fileName #给所有人添加w权限
chmod o-x fileName #给其他人删除x权限
```

##### 4.目录和文件的权限意义

1. 权限对文件的意义

​        r:读取

​        w:可写，编辑

​        x:可执行 ，一个文件是否可以执行，不是看文件后缀名，而是是否具有`x`权限

2. 权限对目录的意义

​       目录:存储文件名列表

​        r:读取目录结构表

​        w:改动目录结构

​         x:用户能为进入该目录能否成为工作目录

##### 5.Linux文件种类和扩展名

在Linux中，要树立一切皆文件的思想

| 描述              |                     文件种类                     |
| ----------------- | :----------------------------------------------: |
| 纯文本文件(ASCII) |                                                  |
| 二进制文件        |                                                  |
| 数据文件          |                  特定格式的文件                  |
| 目录              |                                                  |
| 链接文件          |                类似window快捷方式                |
| 区块设备文件      | 存储数据，以提供系统随机存储的接口设备，例如磁盘 |
| 字符设备文件      |            串行设备文件，例如键盘输入            |
| 数据接口文件      |           网络上的数据交换，例如socket           |

文件扩展名

一个文件能不能被执行，与属性有关，而与文件名没有关系。

#### 4.目录与路径

##### 1.相对路径和绝对路径

绝对路径:一定从根目录`/`写起

相对路径:不由`/`写起

##### 2.目录相关操作

`. `: 此层目录

`..`: 上一层目录

`~`:当前用户的home目录

1. cd 切换目录

```bash
cd 相对路径/绝对路径
```

2. pwd 显示当前目录
3. mkdir 创建一个新的目录

```bash
mkdir [-mp] dirName
# -m 设置权限
# -p 递归创建目录rmdir
```

4. 删除目录

```bash
rmdir [-p] dirName
#-p 连同上一层空的目录也一起删除
```

##### 3.文件操作

1. cp 复制文件或目录

```bash
cp [-alr] source destination
#-a 保持destinamtion属性和权限不变
#-l 创建链接文件
#-r 递归复制文件
cp [options] source1 souce2 ... dirctory
# 当有多个文件被复制时，目标文件必须是
```

2. rm 删除文件或目录

```bash
rm [-fir] 文件或目录
#-f 强制删除
#-i 删除提示
#-r 递归删除
```

3. mv 移动文件与目录，或重命名 

```bash
mv [-fiu] source destination
#-f 强制
#-i 提示
#-u 若目标文件已经存在，且source比较新，才会更新(update)
mv [options] source1 source2 ... directory
```

#### 5.文件内容查看

##### 1.cat

```bash
cat [-n] file #-n显示行号
```

##### 2.翻页

```bash
more file
#space 向下翻一页
#Enter 下一行
#:f 显示文件名和当前行数
#q 退出more 

less file
#space 向下翻一页
#pagedown 下一页
#pageup 上一页
#/字符串 向下查找字符串
#?字符串 向上查找字符串
```

##### 3.数据截取

```bash
head [-n number] file #-n number 显示前多少行
head [-n -number] file #后多少行不打印

tail [-n number] file #-n number 显示后多少行
tail [-n +number] file #只列出多少行后数据
```

