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

