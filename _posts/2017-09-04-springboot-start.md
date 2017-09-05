---
layout: post
title: Spring Boot入门篇
categories: SpringBoot
description: 快速入门Spring Boot，创建一个简单的Http请求处理。
keywords: SpringBoot
---

## 快速入门

废话少说

#### 构建项目

1. 打开[http://start.spring.io](http://start.spring.io)
2. 选择Maven作为构建方式，Java语言开发，版本号1.5.6

![spring_start.png](/images/blog/spring_start.png)

3. 点击Generate Project会下载一个zip文件到你的电脑中。
4. 解压zip文件，你可以使用eclipse导入Meven项目的方式打开工程

#### 项目结构

* src/main/java Java文件，程序入口DemoApplication.java
* src/man/resources 配置文件目录 application.properties
* src/test 单元测试目录

#### 引入web模块

pom.xml文件里已经引入了两个模块

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter</artifactId>
	</dependency>

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
</dependencies>
```

* spring-boot-starter ：核心模块
* spring-boot-starter-test ：测试模块

引入Web模块，需添加spring-boot-starter-web

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

#### 编写HelloWorld服务

* 创建package com.example.demo.controler
* 创建HelloController类

```java
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello() {
        return "Hello Spring Boot.";
    }
}
```

* 启动主程序DemoApplication.java 打开浏览器访问http://localhost:8080/hello，可以看到页面输出Hello Spring Boot.

## 参考

* [Spring Boot](http://projects.spring.io/spring-boot/)
* [示例代码](https://github.com/zhousuhang/springboot/tree/master/springboot-start)
