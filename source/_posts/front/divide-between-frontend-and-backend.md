---
title: "前后端分离思考与实践"
tags:
- 前端
categories:
- 前端
catalog: true
cover: "/images/header_img/header-9.jpg"
date: 2018-10-18 20:51:24
---

### 传统技术

前后端的代码存放在同一个工程目录下，前后端工程师进行开发时，都必须把整个项目导入到开发工具中（像myEclipse和IntelliJ IDEA等）

一方面前端在开发之前需要花费大量的时间来部署开发环境，如果后端上传错了文件，整个系统启动不起来，前端就只能干等着，前后端耦合性很大，

另一方面使用myEclipse这样的开发工具开发前端项目操作上不熟悉，开发效率很低

### 什么是前后端分离

这几年前后端分离被提到的越来越多，在网上查什么是前后端分离，基本是说后台只提供数据api，与用户的交互操作前端来实现。

我们现在的工作模式，前端也是通过ajax请求后台数据，前端的代码单独放在工程目录的一个文件夹中，不与后台的代码耦合，这算不算前后端分离。

前后端分离是前后端代码库分离，前端代码中有可以进行Mock测试(通过构造虚拟测试对象以简化测试环境的方法)的伪后端，能支持前端的独立开发和测试。

而后端代码中除了功能实现外，还有着详细的测试用例，以保证API的可用性，降低集成风险。

### 跨域问题

1.react当中解决跨域问题用proxy可以实现

对于使用creat-react-app构建的项目，可以直接在package.json下配置，具体如下

```json
1 "proxy": "http://api.xxxx.com"  
```

如果同时使用多个域，配置如下

```json
//package.json中加入  
"proxy": {  
	"/api/RoomApi": {  
    "target": "http://open.douyucdn.cn",  
    "changeOrigin":true  
  },  
  "/api/v1":{  
    "target":"http://capi.douyucdn.cn",  
    "changeOrigin":true  
  }  
}  
```

2.使用nodejs中的http-proxy-middleware插件

3.使用jsonp，但是不支持post请求方式

4.后台配置cors，但是ie67完全不兼容，ie89需要做一些特殊处理，ie10以上才能使用

后两种需要后台大拿配合

好了跨域的问题解决了，那么如果是react怎么请求后台数据？

### 数据请求

ajax、axios、fetch

jquery ajax是使用最多的一种方式，问题是jQuery文件太大，react中单纯的使用ajax就引入jquery不太合理

Axios 是一个基于promise的HTTP库，可以用在浏览器和node.js中。简单易用，功能强大。兼容性方面要低于jQuery的ajax，支持ie9以上。提供了很多并发请求的接口，方便了很多。

fetch更加底层，写法很方便，缺点是只对网络请求报错，对400，500都当作成功的请求，需要封装处理。

### 上线统一部署

前端代码开发完成后如何与后台代码统一部署呢，这里就用到了webpack之类的打包工具

使用打包工具可以把前端项目打包成静态压缩文件，即一个index.html一个css一个js压缩文件

然后把他们放在后台工程目录里面运行整个项目就行了，这里要注意文件路径问题，并且之前的跨域处理也要去掉。

对于使用creat-react-app构建的项目，可以使用npm run build来打包

到此，整个使用react的流程就基本搞清了，接下来就是实际的开发工作，react的使用，es6的语法都是重中之重