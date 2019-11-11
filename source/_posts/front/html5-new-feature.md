---
title: "HTML5新特性"
tags:
- JavaScript
- Html
categories:
- 前端
catalog: true
keywords: 'Java,annotation'
description: 'HTML5令人兴奋的新特性'
cover: "/images/header_img/header-10.jpg"
date: 2019-08-07 23:57:24
---

2014年10月29日，W3C(万维网联盟)宣布，HTML5标准规范制定完成。作为一种超文本标记语言，HTML已经成为了Web上使用的通用标记语言，而在这次HTML5的规范中，为开发者带来了一些令人兴奋的新特性。

下面简单地介绍下这些新特性，包括但不限于：

- 语义化标签
- 增强型表单
- DOM扩展
- 原生拖放
- 媒体元素
- Web Socket
- Web Storage
- 地理位置
- canvas绘图

### 语义化标签

HTML 语义化是指仅仅从 HTML 元素上就能看出页面的大致结构，比如需要强调的内容可以放在 `<strong>`标签中，而不是通过样式设置 `<span>`标签去做。不同浏览器对 HTML 元素的解析可能有差异，HTML 语义化便是在抛开样式之后，页面能有一个友好的展示效果。我们力求让页面有良好的结构，让页面的元素有含义，同时利于被搜索引擎解析，利于 SEO。HTML 语义化的建议：

- 少使用无意义的 `<div>`、`<span>`标签；

- 在 `<label>`标签中设置 for 属性和对应的 `<input>`关联起来；

- 设置`<img>`标签的 alt 属性，给`<a>`标签设置 title 属性，利于 SEO；

- 在页面的标题部分使用`<h1>`~`<h6>` 标签，不需要给它们加多余的样式；

- 与表单、有序列表、无序列表相关的标签不要单独使用。

HTML5 也新增了一些语义化的元素，我们通过标签名就能判断标签内容。

语义元素的名称其实也很好理解，下面是它们的作用和用法：

- `<header>`标签通常放在页面或页面某个区域的顶部，用来设置页眉；

- `<nav>`标签可以用来定义导航链接的集合，点击链接可以跳转到其他页面；

- `<article>`标签中的内容比较独立，可以是一篇新闻报道，一篇博客，它可以独立于页面的其他内容进行阅读；

- `<section>`标签表示页面中的一个区域，通常对页面进行分块或对内容进行分段，`<section>`标签和 `<article>`标签可以互相嵌套；

- `<aside>`标签用来表示除页面主要内容之外的内容，比如侧边栏；

- `<footer>`标签位于页面或页面某个区域的底部，用来设置页脚，通常包含版权信息，联系方式等。

### 增强型表单

在HTML5前的表单标签中，对于一些功能支持的不够好，比如：文本框提示信息、表单校验、日期选择控件、颜色选择控件、范围控件、进度条、标签跨表单等功能，HTML5中这些常用的基本功能直接加到了新的表单标签中。

新的表单中添加了很多输入型控件，比如：**number**、**url**、**email**、**range**、**color**、**date**等，通过input的type属性使用。例如：

```html
<input type="number" name="demo" min="1" max="100" step="2"/>
```

同时，还添加了**placeholder**、**required**、**pattern**、**min**、**max**、**height**、**width**等表单属性。

- placeholder 提供对输入域的提示值
- required 规定表单提交前输入域是否必填
- pattern 规定用于验证input域的正则表达式
- min 规定输入域允许的最小值
- max 规定输入域允许的最大值
- multiple 输入域可以选择多个值，适用于email和file类型

HTML5标准的表单中添加了很多功能，虽然这些功能都是之前常用的功能，但对于开发者来说，这确实是一件很不错的事情。

### DOM扩展

HTML5增加的`getElementByClassName()`方法是最受人欢迎的一个方法，可以通过document对象及所有HTML元素调用该方法。

```js
//取得类中包含"username"和"current"的元素
var allCurrentUsernames = document.getElementByClassName("username current")

//取得id为"myDiv"的元素中带有类名"selected"的所有元素
var selected = document.getElementById("myDiv").getElementsByClassName("selected")
```

使用这个方法可以更方便地为带有某些类的元素添加事件处理程序，而不必再局限于使用ID或标签名(**getElementsByTagName**)。

HTML5规定可以为元素添加非标准的属性，但要加前缀**data-**，为元素提供与渲染无关的信息。

```html
<div id="div" data-age="2019" data-name="James"></div>
```

可以通过元素的**dataset**属性访问自定义属性的值。

```js
var div = document.getElementById("div")
//取得自定义属性的值
var age = div.dataset.age;
var name = div.dataset.name;
```

HTML5还为DOM作了其他扩展，包括classList属性、焦点管理、HTMLDocument变化、字符集属性、插入标记等。

### 原生拖放

最早在网页中引入JavaScript拖放功能的是IE4，HTML5以IE的实例为基础制定了拖放规范。拖放事件可以控制拖放相关的各个方面，拖动某元素时，将依次触发下列事件：

- dragstart
- drag
- dragend

拖放通常伴随着数据变化，为了在拖放操作时实现数据变换，IE5引入了**dataTransfer**对象，它是事件对象的一个属性，用于从被拖动元素向放置目标传递字符串格式的数据。dataTransfer对象有两个主要方法：**getData()**和**setData()**，getData()可以获取由setData()保存的值。

```js
//设置和接收文本数据
event.dataTransfer.serData("name", "James")
var name = event.dataTransfer.getData("name")
```

同时，还能通过dataTransfer来确定被拖动的元素及作为放置目标的元素能够接收什么操作。

默认情况下，图像、链接和文本是可以拖动的，HTML5为所有HTML元素规定了一个**draggable**属性，表示元素是否可以拖动。

```html
<img src="smile.gif" draggable="false" alt="smile"/>
<div draggable="true">James</div>
```

### 媒体元素

HTML5新增了两个与媒体相关的标签，让开发人员不必依赖任何插件就能在网页中嵌入跨浏览器的音频和视频内容，这两个标签就是`<audio>`和`<video>`。

```html
<!-- 视频 -->
<video src="video.mp4" id="myVideo">Video Player</video>

<!-- 音频 -->
<audio src="song.mp3" id="myAudio">Audio Player</audio>
```

使用这两个元素时，至少要在标签中包含src属性，指向要加载的媒体文件。并非所有的浏览器都支持所有媒体格式，所以可以指定多个不同的媒体来源，此时使用`<source>`元素而不用指定src属性。

`<audio>`和`<video>`包含很多属性，包括**autuplay**、**controls**、**src**等，还可以触发很多事件，让开发人员在使用少量HTML和JavaScript的情况下编写出自定义的音频/视频播放器。

### Web Storage

Web Storage的目的是克服由cookie带来的一些限制，当数据需要被严格控制在客户端上时，无须持续地将数据发回服务器。Web Storage的两个主要目标是：

- 提供一种在cookie之外存储会话数据的途径
- 提供一种存储大量可以跨会话存在的数据的机制

Web Storage常用的两个对象分别是localStorage和sessionStorage。

**sessionStorage**对象存储某个特定会话的数据，该数据只保持到浏览器关闭。因为sessionStorage绑定于某个服务器会话，所以文件在本地运行时是不可用的。下面展示了怎么使用sessionStorage:

```js
sessionStorage.setItem("name", "James");
var name = sessionStorage.getItem("name")
```

**localStorage**对象在HTML5规范中作为持久保存客户端数据的方案，目的是跨越会话存储对象，但有特定的访问限制。要访问同一个localStorage对象，页面必须来自同一个域名(子域名无效)，使用同一种协议，在同一个端口上。

```js
localStorage.setItem("name", "James");
var name = localStorage.getItem("name")
```

与其他客户端数据存储方案类似，Web Storage同样也有限制，这些限制因浏览器而异。对于localStorage和sessionStorage，有的浏览器会设置每个来源5MB的限制，有的浏览器会设置2.5M的限制。

### Web Sockets

Web Sockets的目标是在一个单独的持久连接上提供全双工、双向通信。使用标准的HTTP服务器无法实现Web Sockets，只有支持这种协议的专门服务器才能正常工作。

未加密的连接不再是http://而是**ws://**，加密的连接也不再是https://而是**wss://**。使用自定义协议而不是HTTP协议的好处是，能够在客户端和服务器之间发送非常少量的数据，而不必担心HTTP那样字节级的开销。

要创建Web Socket，先实现一个WebSocket对象并传入要连接的URL:

```js
var socket = new WebSocket("ws://www.example.com/")
```

Web Socket打开之后，就可以通过连接发送和接收数据。要向服务器发送数据，使用send()方法并传入任意字符串，例如：

```js
socket.send("Hello World");
```

对于复杂的数据结构，在通过连接发送前，必须进行序列化:

```js
//将数据序列化为一个JSON字符串，再发送到服务器
const message = {
  name: "James",
  age: "2019",
  date: "2019-01-01"
}
socket.send(JSON.stringify(message));
```

接下来，服务器要读取其中的数据，就要解析接收的JSON字符串。当服务器向客户端发来消息时，WebSocket对象就会触发message事件。

```js
socket.onmessage = function(event) {
 const data = event.data;
 
 //处理数据
}
```

要关闭Web Socket连接，可以在任何时候调用close()方法。

```js
socket.close();
```

### 地理位置

通过地理定位(geolocation)API，JavaScript代码能够访问到用户的当前位置信息。当然，访问之前用户必须同意共享其地理位置信息。

Geolocation API在浏览器中的实现是**navigator.geolocation**对象，这个对象包含3个方法。第一个方法是**getCurrentPosition()**，调用这个方法就会触发请求用户共享地理定位信息的对话框。成功会接收到一个Position对象参数，该对象有两个属性：**coords**和**timestamp**。coords对象中将包含下列与位置相关的信息：

- latitude：纬度
- longitude：经度
- accuracy：经、纬度坐标的精度，以米为单位

```js
navigator.geolocation.getCurrentPosition(position => {
  drawMapAt(position.coords.latitude, position.coords.longitude);
}, error => {
  console.log("Error Code:" + error.code);
  console.log("Error Message:" + error.message);
});
```

如果希望跟踪用户的位置，可以使用**watchPosition**()方法。这个方法与getCurrentPosition()方法基本相同，第一次调用，执行成功回调或者错误回调，然后，watchPosition()就地等待系统发出位置已改变的信号（它不会自己轮询位置）。

### Canvas绘图

HTML5添加的最受欢迎的功能就是`<canvas>`元素。这个元素负责在页面中设定一个区域，然后就可以通过JavaScript动态地在这个区域中绘制图形。

`<canvas>`元素最早是苹果公司推出的，当时主要用在其Dsahboard文件中（最新的Mac已经弃用了这个软件）。

要使用`<canvas>`元素，必须先设置其width和height属性，指定可以绘图的区域大小。出现在开始标签和结束标签中的内容是后备信息，如果浏览器不支持`<canvas>`元素，就会显示这些信息。

```html
<canvas id="drawing" width="200" height="200">A drawing of something.</canvas>
```

### 结语

上面只是简单地介绍了下HTML5所带来的一些新特性，浅尝则止并不深入，这些新特性在平时的开发中也给我们带来了很大的便利性。

如果想深入地了解这些特性，建议去[Mozilla](https://developer.mozilla.org/zh-CN/docs/Web/HTML)官方网站阅读相关文档。