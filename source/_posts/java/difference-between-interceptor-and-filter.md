---
title: "Interceptor和Filter的区别"
tags:
- Java
- Spring
categories:
- Java
catalog: true
cover: "/images/header_img/header-5.jpg"
date: 2019-04-02 10:51:24
---

![mvc](/images/A6hCPs.png)

#### Interceptor

主要作用：拦截用户请求，进行处理，比如判断用户登录情况、权限验证，只要针对Controller请求进行处理，是通过**HandlerInterceptor**。

Interceptor分两种情况，一种是对会话的拦截，实现spring的HandlerInterceptor接口并注册到mvc的拦截队列中，其中**preHandle()**方法在调用Handler之前进行拦截(上图步骤**3**)，**postHandle()**方法在视图渲染之前调用(上图步骤**5**)，**afterCompletion()**方法在返回相应之前执行；另一种是对方法的拦截，需要使用@Aspect注解，在每次调用指定方法的前、后进行拦截。

#### Filter

主要作用：过滤字符编码、做一些业务逻辑判断，主要用于对用户请求进行预处理，同时也可进行逻辑判断。

Filter在请求进入servlet容器执行service()方法之前就会经过filter过滤（上图步骤**1**），不像Intreceptor一样依赖于springmvc框架，只需要依赖于servlet。Filter启动是随WEB应用的启动而启动，只需要初始化一次，以后都可以进行拦截。

Filter有如下几个种类：

- 用户授权Filter：检查用户请求，根据请求过滤用户非法请求；
- 日志Filter：记录某些特殊的用户请求；
- 解码Filter：对非标准编码的请求解码。

#### Filter和Interceptor的区别

1. Filter是基于函数回调（doFilter()方法）的，而Interceptor则是基于Java反射的（AOP思想）。
2. Filter依赖于Servlet容器，而Interceptor不依赖于Servlet容器。
3. Filter对几乎所有的请求起作用，而Interceptor只能对action请求起作用。
4. Interceptor可以访问Action的上下文，值栈里的对象，而Filter不能。
5. 在action的生命周期里，Interceptor可以被多次调用，而Filter只能在容器初始化时调用一次。
6. Filter在过滤是只能对request和response进行操作，而interceptor可以对request、response、handler、modelAndView、exception进行操作。