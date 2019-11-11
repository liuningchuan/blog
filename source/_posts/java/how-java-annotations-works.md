---
title: "Java中的注解是怎样工作的?"
tags:
- Java
categories:
- Java
catalog: true
cover: "/images/header_img/header-8.jpg"
date: 2019-06-13 10:51:24
---



### 什么是注解

用一个词就可以描述注解，那就是元数据，即一种描述数据的数据。所以，可以说注解就是源代码的元数据。比如，下面这段代码：

```java
@Override
public String toString() {
return "This is String Representation of current object.";
}
```

上面的代码中，重写了toString()方法并使用了@Override注解。

事实上，@Override告诉编译器这个方法是一个重写方法(描述方法的元数据)，如果父类中不存在该方法，编译器便会报错，提示该方法没有重写父类中的方法。如果不小心拼写错误，例如将toString()写成了toStrring(){double r}，而且也没有使用@Override注解，那程序依然能编译运行，但运行结果会和期望的大不相同。

Annotation是一种应用于类、方法、参数、变量、构造器及包声明中的特殊修饰符。它是一种由JSR-175标准选择用来描述元数据的一种工具。使用注解有助于阅读程序。

### 为什么要引入注解

使用Annotation之前(甚至在使用之后)，XML被广泛的应用于描述元数据。不知何时开始一些应用开发人员和架构师发现XML的维护越来越糟糕了。他们希望使用一些和代码紧耦合的东西，而不是像XML那样和代码是松耦合的(在某些情况下甚至是完全分离的)代码描述。如果你在Google中搜索“XML vs. annotations”，会看到许多关于这个问题的辩论。最有趣的是XML配置其实就是为了分离代码和配置而引入的。上述两种观点可能会让你很疑惑，两者观点似乎构成了一种循环，但各有利弊。下面我们通过一个例子来理解这两者的区别。

假如你想为应用设置很多的常量或参数，这种情况下，XML是一个很好的选择，因为它不会同特定的代码相连。如果你想把某个方法声明为服务，那么使用Annotation会更好一些，因为这种情况下需要注解和方法紧密耦合起来，开发人员也必须认识到这点。

另一个很重要的因素是Annotation定义了一种标准的描述元数据的方式。在这之前，开发人员通常使用他们自己的方式定义元数据。例如，使用标记interfaces，注释，transient关键字等等。每个程序员按照自己的方式定义元数据，而不像Annotation这种标准的方式。

### 注解是如何工作的

当注解标注到某个类或者方法或者某个成员变量或者某个输入参数上的时候，一定有一个对应的机制来对注解标注的类、方法、成员变量和参数进行某些处理。

编写Annotation非常简单，注解本身也可以说是一个类。可以将Annotation的定义同接口的定义进行比较。看个例子，标准的注解@Override：

```java
package java.lang;

import java.lang.annotation.*;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

可以看见，@Override注解似乎什么都没做，那它是如何检查在父类中有一个同名的函数？@Override注解的定义仅仅是元数据，不包含业务逻辑，而实现业务逻辑的就是注解的用户。

对于@Override注解，它的用户就是JVM虚拟机，工作在字节码层面，在编译阶段进行检查，其处理机制主要是JVM内部处理。

再例如Spring中的 **@Service** 注解，Spring在启动IOC容器的时候会对每个类进行扫描，把所有标注@Component及其子注解如@Service的类进行Bean处理。

### 编写自定义的注解

JDK5.0版本在 java.lang.annotation提供了四种元注解，专门注解其他的注解：

- @Documented – 是否将注解信息添加在java文档中

- @Retention – 定义该注解的生命周期

- @Target –注解用于什么地方

- @Inherited – 是否允许子类继承该注解

**@Retention** 注解定义该注解的生命周期，在什么阶段丢弃，

- RetentionPolicy.SOURCE – 在编译阶段丢弃。这些注解在编译结束之后就不再有任何意义，所以它们不会写入字节码。@Override, @SuppressWarnings都属于这类注解。

- RetentionPolicy.CLASS – 在类加载的时候丢弃，在字节码文件的处理中有用。注解默认使用这种方式。

- RetentionPolicy.RUNTIME – 始终不会丢弃，运行期也保留该注解，因此可以使用反射机制读取该注解的信息。自定义的注解通常使用这种方式。

**@Target** – 表示该注解用于什么地方。如果不明确指出，该注解可以放在任何地方。属性的注解是兼容的。

- ElementType.TYPE:用于描述类、接口或enum声明

- ElementType.FIELD:用于描述实例变量

- ElementType.METHOD

- ElementType.PARAMETER

- ElementType.CONSTRUCTOR

- ElementType.LOCAL_VARIABLE

- ElementType.ANNOTATION_TYPE 另一个注释

- ElementType.PACKAGE 用于记录java文件的package信息

那么，注解的内部到底是如何定义的呢？Annotations只支持基本类型、String及枚举类型。注释中所有的属性被定义成方法，并允许提供默认值。

定义一个 **@AuthCheck** 注解，作用在SpringMVC controller的方法上。

```java
package com.liuning.sys.auth;

import static java.lang.annotation.ElementType.METHOD;

import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Documented
@Target(METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface AuthCheck {
	String value() default "";
}
```

注解中只有一个属性，可以直接命名为“value”，使用时无需再标明属性名。

在Spring的拦截器**HandlerInterceptor**中实现@AuthCheck注解的业务逻辑，这里使用了反射机制。

```java
@Override
public boolean preHandle(HttpServletRequest request, 
                         HttpServletResponse response, 
                         Object handler) throws Exception {
	HandlerMethod hm = (HandlerMethod) handler;
	AuthCheck ac = hm.getMethodAnnotation(AuthCheck.class);
	if (ac != null) {
		if (ac.value() == "Login") {
			//进行业务逻辑操作
		}
	}
	return true;
}
```

下面的例子演示了如何使用上面的注解。使用了该注解的方法在用户调用的时候会需要用户已登录。

```java
@PostMapping("/list")
@AuthCheck(value = "Login")
public List<User> getUserList(@RequestBody @Valid User user) {
    
    return userService.getUserList(user);
}
```


