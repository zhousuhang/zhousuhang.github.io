---
layout: post
title: SpringBoot之Mybatis
categories: SpringBoot
description: SpringBoot集成Mybatis的两种方式，注解版和xml配置文件版
keywords: SpringBoot
---

## 注解版

### 在pom.xml文件中加入mybatis依赖

```xml
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.3.1</version>
</dependency>
```
### 编写Mybatis的mapper文件

```java
@Mapper
public interface BookMapper {

	@Select("select * from book where name = #{name}")
	Book findByName(@Param("name")String name);

	@Insert("insert into book(name, author) values(#{name}, #{author})")
	int insert(@Param("name") String name, @Param("author") String author);

	@Select("select * from book")
	List<Book> getAllBooks();

	@Delete("delete from book")
	void deleteAll();

	Book findById(Integer id);
}
```

### 单元测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class BookMapperTest {
	@Autowired
	private BookMapper bookMapper;

	@Before
	public void setUp() {
		bookMapper.deleteAll();
	}

	@Test
	@Rollback
	public void test() {
		bookMapper.insert("a", "a");
		bookMapper.insert("b", "b");
		bookMapper.insert("c", "c");
		bookMapper.insert("d", "d");
		bookMapper.insert("e", "e");
		bookMapper.insert("f", "f");

		Assert.assertEquals("a", bookMapper.findByName("a").getAuthor());
		Assert.assertEquals("b", bookMapper.findByName("b").getAuthor());
		Assert.assertEquals(6, bookMapper.getAllBooks().size());
		Assert.assertEquals("b", bookMapper.findById(bookMapper.findByName("b").getId()).getAuthor());
	}
}
```

## xml配置版

同注解版一样引入依赖

### 添加mybatis-config.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<configuration>
	<typeAliases>
		<typeAlias alias="Integer" type="java.lang.Integer" />
		<typeAlias alias="Long" type="java.lang.Long" />
		<typeAlias alias="HashMap" type="java.util.HashMap" />
		<typeAlias alias="LinkedHashMap" type="java.util.LinkedHashMap" />
		<typeAlias alias="ArrayList" type="java.util.ArrayList" />
		<typeAlias alias="LinkedList" type="java.util.LinkedList" />
	</typeAliases>
</configuration>
```

### application.properties中添加配置

```
# mybatis
mybatis.config-locations=classpath:mybatis/mybatis-config.xml
mybatis.mapper-locations=classpath:mybatis/mapper/*.xml
```

### 编写Mybatis的mapper文件

上面的BookMapper中已经加入了一个方法Book findById(Integer id); 这个方法上面我们没有加注解，接下来我们为这个方法添加xml配置。

### 添加BookMapper.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.zhousuhang.demo.springboot.domain.BookMapper">
	<resultMap id="BaseResultMap" type="com.zhousuhang.demo.springboot.domain.Book">
		<id column="id" property="id" jdbcType="INTEGER" />
		<result column="name" property="name" jdbcType="VARCHAR" />
		<result column="author" property="author" jdbcType="VARCHAR" />
	</resultMap>

	<sql id="Base_Column_List">
		id, name, author
	</sql>

	<select id="findById" parameterType="Integer" resultMap="BaseResultMap">
		SELECT
		<include refid="Base_Column_List" />
		FROM book
		WHERE id = #{id}
	</select>
</mapper>
```
至此我们已经可以直接去调用Book findById(Integer id);方法了，上面的单元测试类已经包含了该方法的测试。

## 参考

* [示例代码](https://github.com/zhousuhang/springboot/tree/master/springboot-start)
