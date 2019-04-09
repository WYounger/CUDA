### 测试Git

基本使用方法

1. 添加文件到仓库中

```shell
git add fileName
```

2. 提交

 ```shell
git commit -m "message"
 ```

3. push到仓库中

```shell
git push origin master
```

4. 查看修改状态

```shell
git status
```

5. 提交修改

重复 add->commit->push

6. 新建文件夹

**git是无法提交空文件夹的**，只需要在新文件夹下创建一个文件，然后直接提交该文件即可。

PS:在提交该文件时，会自动带上新文件夹

7.批量提交修改

```shell
git commit -am "message"
#a: all
```

8. 查看命令帮助

1. 查看所有命令

```shell
git help
```

2. 查看具体命令信息

```shell
git command -help #在windows下可用
#git help command #在windows不下可用
#man git -command #在windows不下可用
```

9. 清屏命令

```shell
reset
```

