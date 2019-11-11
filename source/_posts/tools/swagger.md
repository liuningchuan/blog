---
title: "API文档工具Swagger介绍及使用"
tags:
- Java
- Spring
categories:
- Java
catalog: true
keywords: 'JavaScript,ES6'
description: 'API文档工具Swagger介绍及使用'
cover: "/images/header_img/header-4.jpg"
date: 2019-03-23 10:51:24
---

> Swagger是一个流行的RESTFUL API开发框架，基于JSON/YAML语言，以“开放API声明”（OpenAPI Specification）为基础，对整个API的开发周期都提供了相应的解决方案（包括设计、编码和测试，几乎支持所有语言）。
>

[OpenAPI Specification](https://www.openapis.org/)简称OAS，本身是一个API规范，它用于描述一整套API接口，包括接口请求类型、请求参数等，都会被包括在以Yaml/Json格式编写的文件中。而[Swagger](https://swagger.io/)以OpenAPI Specification为基础，面向RESTFUL API接口，通过描述文件提供标准的接口文档，做到代码与接口文档的一致性。

Swagger文档需要单独维护yaml或json格式的描述文件，而在项目持续迭代过程中，往往修改了代码却忘记更新描述文件，给开发者带来了额外负担。因此由于Spring的流行，springfox项目出现了，用于将swagger集成到spring中。

本文先简要地介绍下OpenAPI Specification规范和Swagger提供的一些开源工具，最后详细介绍怎样在Spring中使用springfox自动生成接口文档。

#### Maven依赖配置

```xml
<!-- swaggr2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.6.1</version>
</dependency>
<!-- swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.6.1</version>
</dependency>
```

#### Configuration类配置

```java
@Configuration
@EnableSwagger2
@EnableWebMvc
public class SwaggerConfig {

    @Bean
    public Docket customDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage(
                        "com.liuning.springswagger.controller"))
                .build();
    }

    private ApiInfo apiInfo() {
        Contact contact = new Contact(
                "LiuNing",
                "https://springfox.github.io/springfox/",
                "599522516@qq.com"
        );

        return new ApiInfoBuilder()
                .title("Swagger接口")
                .description("open-api接口")
                .contact(contact)
                .version("1.1.0")
                .build();
    }
}
```

跑别人的demo，自己加上swagger，但是却访问不了，最后发现是因为默认的路径被 @EnableWebMvc覆盖掉 或者说是 继承WebMvcConfigurerAdapter类后，默认的静态资源路径对swagger-ui.html访问不起作用，
因此要重写  WebConfigurer（继承WebMvcConfigurerAdapter的那个类） 的一个方法。

```java
@Configuration
public class WebMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");

        registry.addResourceHandler("swagger-ui.html")
                .addResourceLocations("classpath:/META-INF/resources/");

        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");

    }
}
```

