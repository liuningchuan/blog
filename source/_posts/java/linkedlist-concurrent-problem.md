---
title: "一个LinkedList导致的Java并发问题"
tags:
- Java
categories:
- Java
catalog: true
cover: "/images/header_img/header-8.jpg"
date: 2020-08-31 23:13:24
---

### 事情经过

最近在使用HttpClient的时候，遇到了下面的两个报错：

```java
java.util.ConcurrentModificationException: null
	at java.util.LinkedList$ListItr.checkForComodification(LinkedList.java:966)
	at java.util.LinkedList$ListItr.next(LinkedList.java:888)
	at org.apache.http.impl.client.HttpClientBuilder.build(HttpClientBuilder.java:1066)
```

```java
java.lang.NullPointerException: null
	at java.util.LinkedList$ListItr.next(LinkedList.java:893)
	at org.apache.http.impl.client.HttpClientBuilder.build(HttpClientBuilder.java:1066)
```

一个`ConcurrentModificationException`（少量），一个`NullPointerException`（大量）。

程序中使用封装的HttpClient工具调用其他系统接口的地方全部报上面这个错，但是使用feign、或者dubbo rpc调用的依然正常调用，场面一度很懵逼。

### 寻找原因

使用万能重启大法暂时解决了这个报错后，打开了项目封装的HttpClient工具类，开始研究代码。

看了一会儿，发现原来在代码里面使用了静态的`HttpClientBuilder`：

```java
/**
 * 创建 httpClientBuilder
 */
private static HttpClientBuilder httpClientBuilder = HttpClientBuilder.create();
```

获取HttpClient时也使用了静态方法：

```java
public static CloseableHttpClient getCloseableHttpClient() {
        return httpClientBuilder.setSSLHostnameVerifier(SSLContextManager.simpleHostnameVerifier)
                .setSSLContext(SSLContextManager.simpleSSLContext)
                .addInterceptorLast(new HttpClientTraceIdInterceptor())
                .build();
}
```

看到这里，罪魁祸首就是我前几天为了实现全链路日志追踪而在`getCloseableHttpClient()`方法中添加的一个`HttpClientTraceIdInterceptor`拦截器了。

由于`HttpClientBuilder`是静态的，每一次http调用就会添加一个相同的拦截器，最后堆里面就会充斥着这个`HttpClientTraceIdInterceptor`拦截器。

但每次添加一个Interceptor最多只会因为请求太多了，把内存撑爆导致内存溢出，又为什么会造成并发问题呢？

### 根本原因



