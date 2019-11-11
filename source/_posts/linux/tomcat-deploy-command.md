---
title: "Tomcat部署常用命令"
tags:
- Java
- Linux
categories:
- Linux
catalog: true
cover: "/images/header_img/header-6.jpg"
date: 2019-06-02 10:51:24
---

### 部署

一般情况下进入 **/bin** 路径下shutdown.sh关闭

```
./shutdown.sh
```

有时候关闭失败需要手动杀掉进程，查看tomcat所有进程

```
ps -ef|grep tomcat 
```

关闭tomcat进程

```shell
kill -9 pid
```

然后将war包放入 **/webapps** 路径下，重新启动即可

```shell
./startup.sh
```

查看端口号8080运行的程序:

```shell
lsof -i :8080
```

### 查看日志

tail命令用于依照要求将指定的文件的最后部分输出到标准设备，通常是终端，假设该文件有更新，tail会自己主动刷新，确保看到的是最新内容。

在 **/logs** 下查看tomcat启动日志:

```shell
tail -f catalina.out
```

查看最后100行日志信息:

```shell
tail -100f catalina.out
```

查看最后100行日志信息，但不刷新

```shell
tail -n 100 catalina.out
```

根据关键词字符串查看相关日志

```shell
tail -f catalina.out | grep "053574ccc432403c9762ac1372a7c7"
```

