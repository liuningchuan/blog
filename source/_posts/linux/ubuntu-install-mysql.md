---
title: "Ubuntu安装MySql"
tags:
- Linux
categories:
- Linux
catalog: true
cover: "/images/header_img/header-4.jpg"
date: 2018-07-29 20:51:24
---

根据MySQL官方说明，在Debian/Ubuntu下安装MySQL很简单，只需要几条命令。首先下载Debian/Ubuntu MySQL APT repository，即APT支持包。

下载地址：https://dev.mysql.com/downloads/repo/apt/.

添加到系统software repository list

```
sudo dpkg -i mysql-apt-config_0.8.10-1_all.deb
```

在安装软件包期间，系统会要求您选择要安装的MySQL服务器版本和其他组件（例如，MySQL Workbench）。 如果您不确定选择哪个版本，请不要更改为您选择的默认选项。 如果您不想安装特定组件，也可以选择none。 在为所有组件做出选择后，选择“确定”以完成发布包的配置和安装。

更新APT repository

```
sudo apt-get update
```

安装MySQL

```
sudo apt-get install mysql-server
sudo apt-get install mysql-client
sudo apt-get install libmysqlclient-dev
```

安装过程中会要求为mysql的root用户设置密码

然后就可以开始/停止MySQL服务了

```
sudo service mysql status
sudo service mysql stop
sudo service mysql start
```

登录MySQL数据库

```
mysql -u root -p
```

登录后可以用一下命令导入备份好的数据库

```
source /home/liuning/Documents/mysqlbackup/mybatis.sql
```

可以选择安装mysql-workbench-community

```
sudo apt-get install mysql-workbench-community
```

安装的时候可能会报错，可以先从官网下载deb包在用dpkg安装。

下载地址：https://dev.mysql.com/downloads/workbench/