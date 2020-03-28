# Spring Boot 配置

- [Spring Boot 配置](#spring-boot-%e9%85%8d%e7%bd%ae)
  - [属性注入](#%e5%b1%9e%e6%80%a7%e6%b3%a8%e5%85%a5)
    - [普通的属性注入](#%e6%99%ae%e9%80%9a%e7%9a%84%e5%b1%9e%e6%80%a7%e6%b3%a8%e5%85%a5)
    - [类型安全的属性注入](#%e7%b1%bb%e5%9e%8b%e5%ae%89%e5%85%a8%e7%9a%84%e5%b1%9e%e6%80%a7%e6%b3%a8%e5%85%a5)


配置文件一般直接放置在resources目录下，Spring Boot中的配置文件有两种格式，properties或者yaml，一般情况下，两者可以随意使用，选择自己顺手的就行了

##  属性注入

### 普通的属性注入

由于 Spring Boot 中，默认会自动加载 application.properties 文件，所以简单的属性注入可以直接在这个配置文件中写。

例如，现在定义一个 Book 类：

```java
public class Book {
    private Long id;
    private String name;
    private String author;
    //省略 getter/setter
}
```

然后，在 application.properties 文件中定义属性：

```properties
book.name=三国演义
book.author=罗贯中
book.id=1
```

**注意**

Book 对象本身也要交给 Spring 容器去管理，如果 Book 没有交给 Spring 容器，那么 Book 中的属性也无法从 Spring 容器中获取到值。

配置完成后，在 Controller 或者单元测试中注入 Book 对象，启动项目，就可以看到属性已经注入到对象中了。

一般来说，我们在 application.properties 文件中主要存放系统配置，这种自定义配置不建议放在该文件中，可以自定义 properties 文件来存在自定义配置。

例如在 resources 目录下，自定义 book.properties 文件，内容如下：

```properties
book.name=三国演义
book.author=罗贯中
book.id=1
```

此时，可以通过@PropertySource 来引入配置：

```java
@Component
@PropertySource("classpath:book.properties")
public class Book {
    @Value("${book.id}")
    private Long id;
    @Value("${book.name}")
    private String name;
    @Value("${book.author}")
    private String author;
    //getter/setter
}
```

这样，当项目启动时，就会自动加载 book.properties 文件。

这只是 Spring 中属性注入的一个简单用法，和 Spring Boot 没有任何关系。

### 类型安全的属性注入

Spring Boot 引入了类型安全的属性注入，如果采用 Spring 中的配置方式，当配置的属性非常多的时候，工作量就很大了，而且容易出错。

使用类型安全的属性注入，可以有效的解决这个问题。

```java
@Component
@PropertySource("classpath:book.properties")
@ConfigurationProperties(prefix = "book")
public class Book {
    private Long id;
    private String name;
    private String author;
    //省略getter/setter
}
```

这里，主要是引入 @ConfigurationProperties(prefix = “book”) 注解，并且配置了属性的前缀，此时会自动将 Spring 容器中对应的数据注入到对象对应的属性中，就不用通过 @Value 注解挨个注入了，减少工作量并且避免出错。
