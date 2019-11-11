---
title: "React中使用fetch实现文件上传下载"
tags:
- 前端
- React
categories:
- 前端
catalog: true
cover: "/images/header_img/header-3.jpg"
date: 2018-11-18 10:51:24
---

在最近的项目中需要实现文件上传下载功能，在以前spring、jsp的项目中实现文件上传很简单，但现在前后端分离，前端使用**React**，后端使用**Spring Boot**，实现就没那么方便了。

前端React使用**fetch**而非传统的**XMLHttpRequest**从后端获取Json数据，那么文件上传自然而然也要使用fetch了。

在react中使用fetch上传文件不难，代码如下：

```html
<input type="file" onChange={this.handleUpload}/>
```

```js
handleUpload = (e) => {
	e.preventDefault();
    
    let file = e.target.files[0];
    const formdata = new FormData();
    formdata.append('file', file);
        
    for (var value of formdata.values()) {
        console.log(value);
    }
    
    const url = 'http://127.0.0.1:8080/file/upload';
    fetch(url, {
        method: 'POST',
        body: formdata,
        headers: {
            "Content-Type": "multipart/form-data"
        }
    }).then(response => return response.json();)
      .catch(error => console.log(error));
};
```

上述实现使用了原生的fetch方法，没有做任何的封装，同时还使用了FormData对象。

在实际操作中，遇到了一个坑，在跨域请求时不能自定义头部，需要去掉headers，否则POST请求会变成OPTIONS请求。

fetch实现文件下载就要相对麻烦一些。

下载原理很简单，就是模拟a标签的点击下载，ajax 不支持下载文件功能，是因为 ajax 只能用来传输字符型数据，所以在过去无法使用 ajax 来下载文件。

fetch可以把 response 保存为 **blob**，下载结束后，为这个 blob 创建一个 URL，跳转到这个URL，或使用 anchor element with download property ，浏览器会弹出保存框。如果文件很大的话，还需要用到 filesystem API，因为 blob 是存在内存中的。

```js
const params = {
    group: group,
    path: path
};
//可以根据需求传特定的一些参数
const downloadUrl = 'http://127.0.0.1:8080/file/download';
fetch(downloadUrl, {
    method: 'POST',
    body: window.JSON.stringify(params),
    credentials: 'include',
    headers: new Headers({
        'Content-Type': 'application/json'
    })
}).then((response) => {
    response.blob().then( blob => {
        let blobUrl = window.URL.createObjectURL(blob);
        //不能直接创建一个<a>标签
        // let a = document.createElement('a_id');
        let a = document.getElementById('a_id');
        //无法从返回的文件流中获取文件名
        // let filename = response.headers.get('Content-Disposition');
        let filename = 'file.txt';
        a.href = blobUrl;
        a.download = filename;
        a.click();
        window.URL.revokeObjectURL(blobUrl);
    });
}).catch((error) => {
    console.log(error);
});
```

```html
 <a id='a_id'></a>
```

这样就在React中使用fetch实现了文件上传下载，先这样，有问题后面再补充。