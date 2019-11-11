---
title: "Ubuntu18.04安装jdk"
tags:
- Linux
categories:
- Linux
catalog: true
cover: "/images/header_img/header-2.jpg"
date: 2018-07-15 20:51:24
---

这两天由于公司发了新电脑，加上对Windows不满已久，于是给旧电脑装了个Ubuntu 18.04 LTS。之前装的Windows和Elementary OS双系统算是放弃了。

发现Ubuntu对就电脑也并不是那么友好，对资源使用率和Windows不相上下，算是入坑了。

首当其冲给Ubuntu装上jdk1.8.0_171，由于以前Linux用得不是很多，这里记录一下，方便以后折腾。

- 官网下载JDK文件**jdk-8u171-linux-x64.tar.gz**  [下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- 在/opt创建一个目录作为JDK的安装目录，我的目录为/opt/java

  ```
  sudo mkdir /opt/java
  ```

- 下载好的JDK文件一般在Downloads目录下，需要将其移动到/opt/java下，Ctrl+Alt+T打开终端

  ```
  cd Downloads
  sudo mv jdk-8u171-linux-x64.tar.gz /opt/java
  ```

- 切换到/opt/java目录下，解压文件。这里同样需要管理员权限

  ```
  cd /opt/java
  sudo tar -zxvf jdk-8u171-linuc-x64.tar.gz
  ```

- 解压完成后需要配置环境变量

  ```
  sudo vim /etc/profile
  ```

  在文件的最后添加以下内容

  ```
  #set Java environment
  export JAVA_HOME=/opt/java/jdk1.8.0_171
  export JRE_HOME=$JAVA_HOME/jre
  export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
  export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
  ```

  上面的`JAVA_HOME`就是安装JDK的目录

- 使用命令使环境变量立即生效

  ```
  source /etc/profile
  java -version
  
  -java version "1.8.0_171"
  -Java(TM) SE Runtime Environment (build 1.8.0_171-b11)
  -Java HotSpot(TM) 64-Bit Server VM (build 25.171-b11, mixed mode)
  ```

  显示JDK版本则说明环境变量配置成功。有时候可能需要重启一下电脑。