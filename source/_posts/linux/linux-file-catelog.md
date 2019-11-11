---
title: "Linux软件安装目录"
tags:
- Linux
categories:
- Linux
catalog: true
cover: "/images/header_img/header-5.jpg"
date: 2018-07-19 20:51:24
---

| 文件类型                         | 安装位置       |
| -------------------------------- | -------------- |
| 用户自己的软件                   | /usr/local     |
| 普通执行程序文件                 | /usr/bin       |
| 服务器执行程序文件和管理程序文件 | /usr/sbin      |
| 应用程序配置文件                 | /etc           |
| 日志文件                         | /var/log       |
| 应用程序文档文件                 | /usr/share/doc |
| 应用程序手册页文件               | /usr/share/man |

Linux 的软件安装目录是也是有讲究的，理解这一点，在对系统管理是有益的

`/usr`：系统级的目录，可以理解为`C:/Windows/`，`/usr/lib`理解为`C:/Windows/System32`。
`/usr/local`：用户级的程序目录，可以理解为`C:/Progrem Files/`。用户自己编译的软件默认会安装到这个目录下。
`/opt`：用户级的程序目录，可以理解为`D:/Software`，opt有可选的意思，这里可以用于放置第三方大型软件（或游戏），当你不需要时，直接`rm -rf`掉即可。在硬盘容量不够时，也可将/opt单独挂载到其他磁盘上使用。

#### 源码放哪里？

`/usr/src`：系统级的源码目录。
`/usr/local/src`：用户级的源码目录