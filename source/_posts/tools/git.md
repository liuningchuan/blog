---
title: "git使用简单总结"
tags:
- Git
categories:
- 工具
catalog: true
cover: "/images/header_img/header-3.jpg"
date: 2018-08-18 20:51:24
---

### 新建分支

```shell
git branch <branch_name>
```

例如新建dev分支并将其推送到远程仓库

```shell
git branch dev
git checkout dev
git push --set-upstream origin dev
```

其中`git checkout dev `命令从master、分支切换到dev分支.

如果出现下面的错误

```
fatal: No destination configured to pus to.
```

说明缺少推送的目标地址，解决方法有两种

- 添加目标地址参数

```
git push git@github.com:xxx/xxx.git
```

- 设置默认参数

```bash
git remote add origin git@github.com:xxx/xxx.git
git push
```

- 查看当前所有分支信息
```shell
git branch -av
```



### 用户名和邮箱地址

- 查看

```shell
git config user.name
git config user.email
```

- 修改

```shell
git config --global user.name "username"
git config --global user.email "email"
```



### 配置ssh key

1. 打开终端，输入

```shell
ssh-keygen -t rsa -C 'xxxx@xxx.com'
```

2. 根据提示输入密码，一路回车，会在用户文件夹的.ssh下生成三个文件：**id_rsa** **id_rsa.pub** **knowm_hosts**
3. 进入Github个人主页--settings--点击**SSH and GPG keys** 
4. 打开id_rsa.pub，将其中的内容复制到github的ssh key 输入框里面
5. 之后就可以正常的用git来同步开发项目了。



创建完仓库之后，将本地项目和该仓库关联的步骤：       

①先cd到本地项目的文件夹，执行：git init      

②添加自述文件：git add README.md      

add所有的文件

```shell
$ git add -u
```

③将代码提交到本地仓库先 ：git commit -m "first commit" //“”是注释，任意写       

④ 将本地项目和远程仓库关联：       git remote add origin https://github.com/xxx(一般是你的邮箱名）/xxx（仓库名称）.git      

将本地项目和远程项目进行合并

```shell
$ git pull --rebase origin master 
```

 ⑤将本地代码push到远程仓库：  git push -u origin master  

把远程仓库的代码合并到本地仓库，但不会提交本地的代码

```shell
$ git pull <远程主机名> <远程分支名>:<本地分支名>
```

因为已经和远程仓库关联，因此用origin代替远程主机名

```shell
$ git pull origin Branch.v0.1.1828_dev:Branch.v0.1.1828_dev
```

push的时候如果暴力一点可以直接

```shell
$ git push
```



### 清除本地分支

Git如果在远程仓库上删除了某一分支，并不会删除本地的远程追踪分支。这时候，可以使用如下命令查看那些分支需要清理：

```bash
$ git remote prune origin --dry-run
```

```bash
Pruning origin
URL: git@10.2.10.22:xxx/xxx.git
 * [would prune] origin/Branch.v1.0.1908
 * [would prune] origin/Branch_dev_bugfix_merchant_h5
```

可以看到，有两个远程分支已经失效，将会被清理，执行

```bash
$ git remote prune origin
```

这样，就完成了无效的远程追踪分支的清理工作。

**需要注意，这里远程追踪分支批位于.git/refs/remote/origin 下的分支，如果有本地分支被check out的话，还需要手动清理**

```bash
$ git branch -vv
  Branch.v1.0.1908_unionpay           [origin/Branch.v1.0.1908_unionpay: gone]
```

**无效的远程追踪分支会以gone来标识**

删除无效的本地分支命令:

```bash
$ git branch -d Branch.v1.0.1908_unionpay  
```



### 添加Tag

打印所有标签

```bash
$ git tag
```

查看对应标签状态

```bash
$ git checkout <版本号>
```

附注标签是一个独立的标签对象，包含了名称时间戳以及标签备注等信息，同时指向对应的commit，定义方法如下：

```bash
$ git tag -a <版本号> -m "<备注信息>"
```

删除本地标签

```bash
$ git tag -d <版本号>
```

同创建本地标签一样，删除了本地标签之后也要同时删除远程仓库的标签

```bash
$ git push origin --delete <版本号>
```

推送本地标签到远程仓库

```bash
$ git push origin --tags
```

推送指定版本的标签

```bash
$ git push origin <版本号>
```



### 撤销操作

#### git revert

提交代码，如果有问题，可以使用`git revert`撤销掉该提交，执行以下命令：

```bash
$ git revert HEAD
```

在当前提交后面新增一次提交，抵消掉上一次提交导致的变化。该命令只能抵消上一个提交，如果想抵消多个提交，必须在命令行依次指定这些提交。

```bash
$ git revert [bdc92c7ab9f82bdcc56] [c6e60b6705359d05dde]
```

git revert命令还有两个参数

- `--no-edit`：执行时不打开默认编辑器，直接使用Git自动生成的提交信息
- `--no-commit`：只抵消暂存区和工作区的文件变化，不产生新的提交

#### git reset

让以前的提交彻底消失，而不是抵消掉，可以使用`git reset`命令。丢弃掉某个提交之后的所有提交。

```bash
$ git reset [c6e60b6705359d05dde]
```

默认情况下，该命令只改变暂存区，不改变工作区的文件。`--hard`参数可以让工作区的文件也回到之前的状态。

```bash
$ git reset --hard [c6e60b6705359d05dde]
$ git reset --hard HEAD
```

 使用该命令，工作区的文件一旦撤销，再也无法找回。

```shell
$ git reset HEAD
```

已经git add的文件，使用该命令恢复成未提交状态。

