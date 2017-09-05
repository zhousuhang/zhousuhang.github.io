---
layout: post
title: SpringBoot之自定义参数、多环境配置及单元测试
categories: SpringBoot
description: 读取自定义参数、通过多环境配置简化环境切换，使用Junit进行单元测试
keywords: SpringBoot
---

## 自定义参数

application.properties文件除了可以设置各个starter模块的预定义配置属性，也可以定义我们需要的自定义属性。
比如我们可以添加如下内容：

```
book.id=1
book.name=Spring Boot Start
book.author=Spring
book.isbn=78709872449
```

然后，我们可以通过@Value注解来加载这些自定义参数：

```java

@Component
public class Book {
	@Value("${book.id}")
	private int id;
	@Value("${book.name}")
	private String bookName;
	@Value("${book.author}")
	private String author;
	@Value("${book.isbn}")
	private String isbn;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getBookName() {
		return bookName;
	}

	public void setBookName(String bookName) {
		this.bookName = bookName;
	}

	public String getAuthor() {
		return author;
	}

	public void setAuthor(String author) {
		this.author = author;
	}

	public String getIsbn() {
		return isbn;
	}

	public void setIsbn(String isbn) {
		this.isbn = isbn;
	}

}

```

使用Junit写一个单元测试来验证一下上面的程序：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class BookTest {
	@Autowired
	private Book book;

	@Test
	public void test() {
		Assert.assertEquals(book.getBookName(), "Spring Boot Start");
		Assert.assertEquals(book.getAuthor(), "Spring");
		Assert.assertEquals(book.getIsbn(), "78709872449");
		Assert.assertEquals(book.getId(), 1);
	}
}

```

## 多环境配置

我们在开发应用的时候，通常需要在不同的环境中进行切换，比如开发、测试、生产等。每个环境的数据库、端口等都不相同，为不同环境打包时手动去修改相应的配置，不但繁琐而且容易发生错误。

在SpringBoot中，多环境配置文件名要满足如下格式  application-{profile}.properties ，其中{profile}对应你的环境标识，如下所示：

* application-dev.properties 开发环境
* application-test.properties 测试环境
* application-prod.properties 生产环境

具体加载哪个文件，可以在 application.properties 文件中来设置。比如 spring.profiles.active=test就会加载 application.properties 配置文件的内容

## Junit测试controller

直接上代码

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootConfiguration
@WebAppConfiguration
public class HelloControllerTest {

	private MockMvc mvc;

	@Before
	public void setUp() throws Exception {
		mvc = MockMvcBuilders.standaloneSetup(new HelloController()).build();
	}

	@Test
	public void test() throws Exception {
		mvc.perform(MockMvcRequestBuilders.get("/hello").accept(MediaType.APPLICATION_JSON))
				.andExpect(MockMvcResultMatchers.status().isOk())
				.andExpect(MockMvcResultMatchers.content().string("Hello Spring Boot."))
				.andDo(MockMvcResultHandlers.print()).andReturn();
	}
}

```

## 示例代码

[GitHub](https://github.com/zhousuhang/springboot/tree/master/springboot-start)

