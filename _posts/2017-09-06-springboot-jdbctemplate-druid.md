---
layout: post
title: SpringBoot之数据库访问（JdbcTemplate、Druid）
categories: SpringBoot
description: SprinbBoot下使用JdbcTemplate访问数据库，使用Druid作为数据库连接池
keywords: SpringBoot
---

## 简介

### JdbcTemplate

JdbcTemplate是Spring封装的数据库访问模版，你可以直接使用@Autowired来注入到你自己的bean中来使用。

### Druid

Drui官方如是说
> Druid是一个JDBC组件库，包括数据库连接池、SQL Parser等组件。DruidDataSource是最好的数据库连接池。

## 配置数据源

### 引入所需依赖

首先，引入jdbc的支持，在pom.xml中加入如下配置：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

加入mysql链接的依赖包，在pom.xml中加入：

```xml
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>
```
在application.properties中配置数据源信息

```
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
# spring.datasource.driver-class-name可不加，spring boot会自动识别
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

### 使用JdbcTemplate操作数据库

创建book表，包含属性name、author

* 定义BookService接口：

```java
public interface BookService {
	void create(String name, String author);

	void deleteByName(String name);

	Integer getBooksCount();

	void deleteAllBooks();
}
```

* BookServiceImpl实现BookService接口，注入JdbcTemplate访问数据库：

```java
@Service
public class BookServiceImpl implements BookService {


	@Autowired
    private JdbcTemplate jdbcTemplate;

	@Override
	public void create(String name, String author) {
		jdbcTemplate.update("insert into book(name, author) values(?, ?)", name, author);
	}

	@Override
	public void deleteByName(String name) {
		jdbcTemplate.update("delete from book where name = ?", name);
	}

	@Override
	public Integer getBooksCount() {
		return jdbcTemplate.queryForObject("select count(1) from book", Integer.class);
	}

	@Override
	public void deleteAllBooks() {
		jdbcTemplate.update("delete from book");
	}

}
```
更多其他数据访问操作的使用请参考：[JdbcTemplate API](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html)

* 创建单元测试用例，验证数据库操作是否正确：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class BookServiceTest {

	@Autowired
    private BookService bookService;

	@Before
    public void setUp() {
		bookService.deleteAllBooks();
    }

	@Test
    public void test() throws Exception {
		bookService.create("a", "a");
		bookService.create("b", "b");
		bookService.create("c", "c");
		bookService.create("d", "d");
		bookService.create("e", "e");

        Assert.assertEquals(5, bookService.getBooksCount().intValue());

        bookService.deleteByName("a");
        bookService.deleteByName("e");

        Assert.assertEquals(3, bookService.getBooksCount().intValue());

    }
}
```

## 使用Druid作为数据库连接池和监控配置

下面来说明如何在 Spring Boot 中配置使用Druid

### 添加依赖

Druid官方已经提供了Spring Boot的起步依赖druid-spring-boot-starter，方便与Spring Boot集成，在pom.xml中添加依赖：

```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid-spring-boot-starter</artifactId>
	<version>1.1.3</version>
</dependency>

```

### 添加配置

配置application.properties

```
#JDBC
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
#可以省略
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
#可以省略
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource

spring.datasource.druid.initialSize=2
spring.datasource.druid.minIdle=2
spring.datasource.druid.maxActive=30

######Druid Monitor######
spring.datasource.druid.WebStatFilter.exclusions=*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*

spring.datasource.druid.StatViewServlet.loginUsername=druid
spring.datasource.druid.StatViewServlet.loginPassword=druid
```
更多配置，请参考[这里](https://github.com/alibaba/druid/blob/master/druid-spring-boot-starter/src/test/resources/config-template.properties)

### 查看监控页面

接下来可以启动项目看看，访问http://localhost:8080/druid 首先输入上面配置的用户名和密码 druid 进行登录：


## 示例代码

[GitHub](https://github.com/zhousuhang/springboot/tree/master/springboot-start)
