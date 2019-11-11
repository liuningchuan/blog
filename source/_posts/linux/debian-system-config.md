---
title: "Debian系统配置使用心得"
tags:
- Linux
categories:
- Linux
catalog: true
cover: "/images/header_img/header-7.jpg"
date: 2018-08-12 20:51:24
---

经过自己作死般不停地瞎折腾，抛弃了Ubuntu又给电脑装上了Debian。

这么些天折腾下来，什么技术没学到，Linux发行版倒是体验了好几种，我想Debian应该是最终的选择了吧，呵呵。

选择Debian的原因也很简单，Ubuntu更多是一个桌面发行版，做了很多桌面优化使得它的界面看上去很漂亮（**重点是我觉得很丑**），对系统的资源占用率相对较Debian要高些。我的电脑又是刚上大学时买的一台很垃圾的华硕笔记本，到现在用着已经很吃力了。

经过比较，安装后Ubuntu后内存占用1.4G左右，而安装Debian后内存占用1G左右，这小小的差距让我心里平衡了一些。Debian的界面看上去清爽了很多，Ubuntu看上去就跟个紫薯精一样，我是真的无法理解Ubuntu的界面漂亮在哪里。

使用Debian后就会发现Ubuntu是多么省事，要想日常用上Debian还得克服重重阻碍。

#### 1.系统窗口最大化最小化

安装好Debian新系统后，第一件事就是打开系统自带的火狐浏览器，发现居然只有关闭按钮而没有最大化最小化按钮，这真的是让我很蛋疼了。

解决方法很简单，在终端输入

```shell
gnome-tweak-tool
```

打开设置窗口：

- 在桌面栏开启桌面图标
- 在窗口栏开启最大化和最小化按钮显示

因为没有开启桌面图标，打开终端都得在应用程序里面找，而且没有安装中文支持，所有的界面都是英文的。

#### 2.添加终端快捷键

Debian默认是没有终端的快捷键的需要自己定义，设置的方法也很简单，打开设置->键盘，滑到底部用户自定义快捷键。

- 名称：Terminal
- 命令：gnome-terminal
- 快捷键：Ctrl + Alt + T

这样终端的快捷键就设置成功。

#### 3.修改软件源设置

用apt-get安装软件包时提示让我插入netinst的光盘，莫名奇妙

```
Media change:please insert the disc labeled
```

这时需要打开`/etc/apt/sources.list`文件，注释掉cdrom那一行，再执行`apt-get update`更新下deb仓库，再使用apt-get安装时就不会搜寻cdrom了。

接着安装vim

```
apt-get install vim

E:Package 'vim-gtk' has no installation candidate
```

真的是坑爹，解决方法就是修改软件源设置

- 备份源文件

```
cp -i /etc/apt/sources.list /etc/apt/sources.list_backup
```

- 打开sources.list

```shell
gedit /etc/apt/sources.list
```

- 在源文件中加入如下阿里巴巴的阿里云服务器的地址并保存

```
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
```

- 更新

```shell
apt-get update
```

这样问题就解决了，安装vim也不会再报错了。

#### 4.安装sudo并添加sudoers

前面的这些操作都是在root用户下操作的，和Ubuntu不同，Debian在安装过程中会让你选择添加root用户和普通用户，这也是我推荐的一种做法。

但是普通用户需要执行一些root命令时总不能每次都`su root`切换到root用户吧？于是需要安装一下sudo

```shell
apt-get install sudo
```

sudo能够完成很多root用户能完成的工作，它们之间也有一定的区别，执行sudo时需要的是普通用户的密码。

安装完sudo执行命令后还是会报错

```
xxx is not in the sudoers file.This incident will be reported.
```

显然，需要在sudoers文件中添加当前用户

- 切换到root，添加sudo文件的写权限

```shell
chmod u+w /etc/sudoers
```

- 编辑sudoers文件

```shell
vim /etc/sudoers
```

找到root ALL=(ALL) ALL,在下面添加xxx ALL=(ALL) ALL (xxx即普通用户名)

- 撤销sudoers文件写权限

```shell
chmod u-w /etc/sudoers
```

这样普通用户就可以使用sudo命令了。

#### 5.安装Chrome浏览器

虽然Debian自带了Firefox浏览器，但Firefox显然无法和Chrome浏览器相抗衡，亲测，在Debian和Ubuntu下，Firefox的打开速度都没有Chrome快，而且Firefox顶部有一个特别难看占面积的边框，果然放弃。

首先，打开终端并运行以下命令下载chrome deb包

```shell
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

然后，安装依赖关系

```shell
sudo apt install libappindicator1 libdbusmenu-glib4 libdbusmenu-gtk4 libindicator7 libpango1.0-0 libpangox-1.0-0
```

最后，使用`dpkg`安装chrome deb包

```shell
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

安装过程中如果报错，可以使用一下命令

```shell
sudo apt-get update
sudo apt-get upgrade
sudo apt-get -f install
```

如果是缺包的话就安装呢相应的依赖包。

#### 6.安装中文支持及搜狗输入法

要支持区域设置，首先要安装locales软件包

```shell
apt-get install locales
```

然后配置locales软件包

```
dpkg-reconfigure locales
```

在界面中选择`zh_CN.UTF-8`即可。

接下来就是中文输入法了，这里选择搜狗输入法，在官网下载deb包

```shell
sudo apt-get install fcitx
sudo dpkg -i sougoupinyin_2.2.0.0108_amd64.deb
```

中间报错可以执行

```shell
sudo apt-get install -f
sudo apt-get install update
```

安装过程中会出现各种各样的坑爹问题，一般来说都是可以解决的。



