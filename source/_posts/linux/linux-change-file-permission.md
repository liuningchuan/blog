---
title: "Linux修改文件权限"
tags:
- Linux
categories:
- Linux
catalog: true
cover: "/images/header_img/header-6.jpg"
date: 2018-07-22 20:51:24
---

Linux系统下经常遇到文件或者文件夹的权限问题，或者是因为文件夹所属的用户问题而没有访问的权限。根据我自己遇到的情况，对这类问题做一个小结。

在命令行使用命令“ll”或者“ls -a”，可以查看文件或者文件的权限：

```shell
-rw-r--r--. 1 root root 6 Nov  9 16:42 a.txt
```

其中“-rw-r--r--”表示权限，一共有十个字符。第一个字符，如果是“**-**”则表示是文件，如果是“**d**”则表示是目录（directory）。后面9个字符每3个字符又作为一个组，则有3组信息（“rw-”、“r--”、“r--”），分别表示所属用户本身具有的权限、所属用户的用户组其他成员的权限、其他用户的权限。

每一组信息如“rw-”,每一个字符都有它自己的特定含义且先后位置是固定的，其中r是读权限、w是写权限、x是可执行权限、-没有对应字符的权限。Linux里面对这些字符设置对应的数值，r是4，w是2，x是1，-是0。上面的“rw-”则是6（=4+2+0），所以最开始a.txt的权限是644，属于root用户组的root用户。

### 修改权限chmod

**改文件的权限**，我想修改文件a.txt的权限为755，则为：

```shell
[root@master my]# ll
-rw-r--r--. 1 root root 6 Nov  9 16:42 a.txt #改之前权限是644
[root@master my]# chmod 755 a.txt
[root@master my]# ll
-rwxr-xr-x. 1 root root 6 Nov  9 16:42 a.txt #改后权限是755
```

**改文件夹的权限**，改之前：

```shell
[root@master test1]# ll
drw-r--r--. 2 root root 4096 Nov  9 16:42 my #改之前文件夹my的权限是644
[root@master test1]# ll my/
-rwxr-xr-x. 1 root root 6 Nov  9 16:42 a.txt #改之前文件夹my的里面的文件权限是755
```

**只改变文件夹本身权限，不改动子文件（夹）**，执行命令修改my文件夹权限为600：

```shell
[root@master test1]# chmod 600 my/  #修改命令
[root@master test1]# ll
drw-------. 2 root root 4096 Nov  9 16:42 my #改my文件夹之后权限是600
[root@master test1]# ll my/
-rwxr-xr-x. 1 root root 6 Nov  9 16:42 a.txt 
#改my文件夹之后里面的文件权限还是755，没有变化
```

**改变文件夹及子目录下所有文件（夹）权限**，执行1.2.1步骤之后，my文件夹和里面的文件权限都是不同的。现在递归修改，都改为统一的权限777：

```shell
[root@master test1]# chmod -R 777 my/ #修改命令，注意中间是大写的R，不是小写
[root@master test1]# ll
drwxrwxrwx. 2 root root 4096 Nov  9 16:42 my #修改后my文件夹权限是777
[root@master test1]# ll my/
-rwxrwxrwx. 1 root root 6 Nov  9 16:42 a.txt #修改后里面的文件变为了777
```

### 修改所属用户和用户组chown

这个和修改文件夹的权限是基本相同的，只不过是把chmod命令换成了chown。

**修改文件所属用户和用户组**，修改a.txt文件所属用户（jay）和用户组（fefjay）：

```shell
chown jay:fefjay a.txt #修改文件所属用户为jay，所属用户组为fefjay
```

**只改文件夹本身所属用户和用户组，不改子文件（夹）**，仅修改文件夹my本身所属用户（jay）和用户组（fefjay）：

```shell
chown jay:fefjay my #修改文件所属用户为jay，所属用户组为fefjay
```

**改变文件夹及所有子文件（夹）所属用户和用户组**，递归修改文件夹my及包含的所有子文件（夹）的所属用户（jay）和用户组（fefjay）：

```shell
chown -R jay:fefjay my #修改文件所属用户为jay，所属用户组为fefjay
```

### 总结

修改单个文件（夹）就用命令：

```shell
chown或chmod    “权限”或“名：组”    文件（夹）名称
```

修改文件夹及子文件夹所有文件就用命令：

```shell
chown或chmod  -R   “权限”或“名：组”    文件夹名称
```

