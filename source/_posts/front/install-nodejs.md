---
title: "Node.js安装"
tags:
- Node.js
categories:
- 工具
catalog: true
cover: "/images/header_img/header-1.jpg"
date: 2018-09-18 10:51:24
---

[官网](https://nodejs.org/en/)下载源码包,解压安装,如果要安装不同版本则只需要改变版本号即可

```bash
wget https://nodejs.org/dist/v8.11.3/node-v8.11.3.tar.gz
tar -zxvf node-v8.11.3.tar.gz
cd node-v8.11.3
./configure
make && make install
```

可能出现报错

![Snipaste_2018-07-17_15-30-17](C:\Users\liuning\Pictures\Saved Pictures\Snipaste_2018-07-17_15-30-17.png)

只需要安装

```
sudo yum -y install gcc automake autoconf libtool make
sudo yum install gcc gcc-c++
```

然后再执行make命令就可以了,时间可能会很长.

配置NODE_HOME，进入profile编辑环境变量 

```
vim /etc/profile
-export NODE_HOME=/usr/local/node/0.10.24
-export PATH=$NODE_HOME/bin:$PATH
source /etc/profile
```

验证是否配置成功

```
node -v
```

npm（node package manager）nodejs的包管理器，用于node插件管理（包括安装、卸载、管理依赖等） ,一般npm会在弄得安装时自动安装

由于npm的官网镜像下载很慢，所以建议先按个cnpm，连接淘宝提供的npm库 

```
npm install -g cnpm
```
#### 全局安装gulp

```
cnpm install gulp -g
gulp -v
```

### 查看已安装的npm包

```
npm list -g --depth 0
```

- 全局安装模块xxx

```
npm install -g xxx
```

- 安装并写入package.json的"dependencies"中

```
npm install xxx --save
```

- 安装并写入package.json的"devDependencies"中

```
npm install xxx --save-dev
```

- 删除xxx模块

```
npm uninstall xxx
```

- 删除全局模块xxx

```bash
npm uninstall -g xxx
```

- 查看npm配置

```
npm config list
```

- 将npm镜像设置为淘宝镜像

```shell
npm config set registry http://registry.npm.taobao.org/
```

- 还原为npm官方镜像

```shell
npm config set registry https://registry.npmjs.org/
```



http://www.cnblogs.com/penghuwan/p/6973702.html

```
npm cache clean --force //强制清除缓存
```

