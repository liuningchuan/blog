---
title: "Ubutnu解决zip中文乱码问题"
tags:
- Linux
categories:
- Linux
catalog: true
cover: "/images/header_img/header-3.jpg"
date: 2018-07-19 20:51:24
---

Ubuntu自带的解压工具zip解压的时候总是中文乱码，使用unar工具可以完美地解决zip解压乱码问题，操作也很简单，一步到位。

- 安装unar解压工具

  ```-
  sudo apt-get install unar
  ```

- 解压相应的zip文件

  ```
  unar document.zip
  ```

瞬间就能解决zip解压中文乱码的问题。