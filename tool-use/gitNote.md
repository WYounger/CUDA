### 测试Git

#### 基本使用方法

##### 1.添加文件到仓库中

```shell
git add fileName
```

   ```shell
#修改文件名
git mv oldFileName newFileName
git commit -m "message"
   ```

##### 2.提交

 ```shell
git commit -m "message"
 ```

##### 3.push到仓库中

```shell
git push origin master #将更新到远程仓库
```

##### 4.查看修改状态

```shell
git status
```

##### 5.提交修改

​        重复 add->commit->push

##### 6.文件夹

​    1.新建文件夹

​         **git是无法提交空文件夹的**，只需要在新文件夹下创建一个文件，然后直接提交该文件即可。

​         PS:在提交该文件时，会自动带上新文件夹

   2.修改文件夹名称

   ```shell
git mv -f oldFolder mewFolder #修改文件名
git add -u newFolder #-u：更新已经追踪的文件和文件夹
git commit -m "message" #提交修改
   ```

##### 7.批量提交修改

```shell
git commit -am "message"
#a: all
```

##### 8.查看命令帮助查看所有命令

1. 查看全部

```shell
git help
```

2. 查看具体命令信息

```shell
git command -help #win下可用
git help command #win下不可用
man git -command #win下不可用
```

##### 9. 清屏命令

```shell
reset
```

##### 10.在md文件中添加图片

1. 首先新建一个图片文件夹，并放入一张图片，然后上传到仓库中
2. 在仓库中找到该图片，并复制该图片的路径（绝对路径）
3. 在需要引用的该图片的md文档位置使用该路径
##### 11.pull
在线在仓库中直接修改文档，后可以用命令同步到本地
```shell
git pull origin master
```

##### 12.分支操作

```bash
git init #初始化一个本地仓库
git add <file> #可以反复使用，添加多个文件 file1 file2...
git commit -m <message> #提交到仓库
git status #查看工作状态

#git add把文件修改添加到暂存区(stage)
#git commit 把暂存区的所有内容提交到当前分支(默认master)

#git管理的是修改。每次修改，如果不用git add到暂存区，那就不会加入到commit

#仅仅针对修改还没有添加到暂存区【工作区恢复】
git checkout --<file> #让文件回到最近一次的git commit 或git add时的状态

#仅仅针对修改添加到暂存区【暂存区恢复】
git reset HEAD <file> #将对暂存区的修改撤销掉，但是工作区修改还未撤销，需要的继续使用git checkout来使工作区复原

#删除文件
git rm <file>
git commit -m <"delete the file">

#本地仓库关联远程仓库
git remote add origin https://github.com//UserName/Repostory.git
git push -u origin master#第一次推送master分支的所有内容
#以后每次本地提交后，就可以使用git push origin master推送最新的修改

#将远程仓库克隆到本地
git clone https/ssh-address

#分支管理
git branch <name> #创建分支
git checkout <name> #切换分支
git checkout -b <name>#创建并切换分支
git merge <name>#切换某分支到当前分支
git branch -d <name>#删除当前分支
```

13.仓库管理

```bash
git remote add origin git@github.com:userName/repository.git
git push -u origin master#首次本次仓库关联远程仓库

git remote remove origin #删除联系
git remote show orgin #查看联系
```

