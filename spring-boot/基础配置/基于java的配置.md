# 基于java的配置

[谈谈spring-boot不同包结构下，同样的类名冲突导致服务启动失败解决方案](https://www.cnblogs.com/bedlimate/p/8660839.html)
[SpringBoot基础篇之重名Bean的解决与多实例选择](https://blog.csdn.net/weixin_33714884/article/details/93029760)
[Spring同名bean覆盖问题](https://blog.csdn.net/ligeforrent/article/details/77237030)

## 1 基于 java 的基本配置

### 1.1 理解
@Configuration和@Bean的用法和理解

@Configuration 可理解为用 spring 的时候xml里面的`<beans>`标签，@Bean 可理解为用 spring 的时候xml里面的`<bean>`标签

**@Component、@Repository、@Service、@Controller是将自定义的、新的class实例化后放入bean容器中管理，而@Configuration和@Bean是将实例化已有的class后放入bean容器中管理**

因此在集成其他框架，如集成shiro权限框架，用@Configuration和@Bean来配置Shiro

```java
@Configuration
public class JdbcConfign {

    @Value("com.mysql.jdbc.Driver")
    private String driverClassName;

    @Value("jdbc://xxxx.xx.xxx/xx")
    private String driverUrl;

    @Value("${root}")
    private String driverUsername;

    @Value("123456")
    private String driverPassword;

    @Bean(name = "dataSource")
    public DataSource dataSource() { // 这里可以单独写成一个方法，也可以将内容写在下面的jdbcTemplate方法中
        BasicDataSource dataSource = new BasicDataSource(); // 定义一个用于返回的对象
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(driverUrl);
        dataSource.setUsername(driverUsername);
        dataSource.setPassword(driverPassword);
        return dataSource; // 最终返回对象
    }

    @Bean
    public JdbcTemplate jdbcTemplate() {
        return new JdbcTemplate(dataSource()); // 在同一个config文件中通过调用方法dataSource得到DataSource实例，在外部bean class 中可以通过@Autowired自动注入
    }

}
```

这样在项目中如下使用，这个jdbcTemplate就是我们在JdbcConfign中配的DataSource

```java
@Autowired
private JdbcTemplate jdbcTemplate;
```

### 1.2 @Bean修饰的方法参数的注入方式

方法参数默认注入方式为Autowired，即先根据类型匹配，若有多个在根据名称进行匹配。

```java
@Configuration
@PropertySource("classpath:db.properties")
public class SpringConfiguration {
    @Bean
    public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
        return new PropertySourcesPlaceholderConfigurer();
    }

    @Bean(name = "dataSource")
    public DataSource dataSource(@Value("${jdbc.driverClass}") String driverClassName,
        @Value("${jdbc.jdbcUrl}") String url, @Value("${jdbc.user}") String username,
        @Value("${jdbc.password}") String password) {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
    @Bean(name = "jdbcTemplate")
    public JdbcTemplate jdbcTemplate(@Qualifier(value = "dataSource") DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }
}
```

### 1.3 问题

一个SpingBoot项目中可能有多个配置类，不同配置类中各个`@Bean`修饰的方法名称不能相同（除非`@Bean`中定义了name属性），否则将报如下错误

![01](./images/01.png)

## 2 基于 java 的自动配置

注意涉及到下面三个注解

- @Component: 注册为由 Spring 管理的类
- @Autowired: 指示 Spring 注入一个依赖对象
- @ComponentScan: 指示Spring在何处查找带有@Component注解的类

### 2.1 构造函数注入

@Autowired 注解用来指导 Spring 我们打算在使用注解的位置注入一个依赖对象。例如，在 Car 构造函数中，我们期望注入一个 Engine 对象，因此，我们给 Car 构造函数添加@Autowired注解。

```java
@Component
public class Car {

    private Engine engine;

    @Autowired
    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.turnOn();
    }
}
```

我们需要创建一个@Configuration 类来指导 Spring 如何自动配置我们的应用程序。我们已经通过@Component 和@Autowired 注解提供了足够的信息，说明如何创建所需的所有 bean。唯一缺少的信息是 Spring 应该在哪里寻找我们的带有@Component注解的 类,并把它注册为对应的bean。@ Componentscan 注释包含一个参数 basePackages，它允许我们将包名称指定为一个 String，Spring 将通过递归搜索来查找@Component 类。

```java
@Configuration
@ComponentScan(basePackages = "com.milo.domain")
public class AutomatedAnnotationConfig {}

ApplicationContext context = new AnnotationConfigApplicationContext(AutomatedAnnotationConfig.class);
Car car = context.getBean(Car.class);   
car.start();
```
### 2.2 字段注入

我们可以将@Autowired 注解应用到所需的字段来实现这一点:

```java
@Component
public class Car {

    @Autowired
    private Engine engine;

    public void start() {
        engine.turnOn();
    }
}
```

### 2.2 Setter注入

@Autowired 注解应用于与字段关联的 setter，setter 注入可能是三种注入技术中最不常见的，尽可能优先使用构造函数注入。

```java
@Component
public class Car {

    private Engine engine;

    public void start() {
        engine.turnOn();
    }

    public Engine getEngine() {
        return engine;
    }

    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

## 3 依赖注入常见问题

- 依赖对象冲突
- 依赖对象间存在循环依赖

### 3.1 依赖对象冲突

在自动依赖注入时，如果有多个符合条件的依赖对象，Spring 就无法确定注入哪一个 Bean，导致报错：

```java
No qualifying bean of type 'com.dzone.albanoj2.spring.di.domain.Engine' available: expected single matching bean but found 2: combustionEngine,electricEngine
```

代码：

```java
public interface Engine {
     void turnOn();
}
```

```java
@Component
public class ElectricEngine implements Engine {
    @Override
    public void turnOn() {
        System.out.println("电动引擎启动");
    }
}
```

```java
public class CombustionEngine implements Engine {
    @Override
    public void turnOn() {
        System.out.println("燃油引擎启动");
    }
}
```

由于我们已经注释了用@Component 实现 Engine 接口的两个实现类（CombustionEngine、ElectricEngine），spring 现在无法确定在实例化 Car 对象时应该使用这两个类中的哪一个来满足 Engine 依赖性。为了解决这个问题，我们必须明确地指示 Spring 使用这两个 bean 中的哪一个。

解决：

#### 3.1.1 使用 @Qualifier 注解

一种方法是给我们的依赖对象命名,并在应用@Autowired注解的地方使用@Qualifier注解来确定注入哪一个依赖对象。

```java
@Component("defaultEngine")
public class CombustionEngine implements Engine {
    // ...代码省略,未改变
}
```

```java
@Component
public class Car {

    @Autowired
    public Car(@Qualifier("defaultEngine") Engine engine) {
        this.engine = engine;
    }

    // ...existing implementation unchanged...
}
```

注意，如果没有显式申明bean名称的类都有一个默认名称，该默认名称就是类名首字母小写。例如，我们的 Combusttionengine 类的默认名称是 combusttionengine

#### 3.1.2 使用 @Primary 注解

如果我们知道默认情况下我们更喜欢一个实现，那么我们可以放弃@Qualifier 注释，直接将@Primary 注释添加到类中。

```java
@Component
@Primary
public class CombustionEngine implements Engine {
    // ...existing implementation unchanged...
}
```

虽然有两种可能性满足 Engine 依赖性，即 CombustionEngine 和 Electricengine，但 Spring 能够根据@Primary 注释决定两种实现中哪一种应该优先使用。

### 3.2 循环依赖

假设我们创建了一个 Foo 类，它的构造函数需要一个 Bar 对象，但是 Bar 构造函数需要一个 Foo 对象。

```java
@Component
public class Foo {

    private Bar bar;

    @Autowired
    public Foo(Bar bar) {
        this.bar = bar;
    }
}
```

```java
@Component
public class Bar {

    private Foo foo;

    @Autowired
    public Bar(Foo foo) {
        this.foo = foo;
    }
}
```

首先，Spring 尝试创建 Foo 对象。在这个过程中，Spring 认识到需要一个 Bar 对象。为了构造 Bar 对象，需要一个 Foo 对象。由于 Foo 对象目前正在构建中(这也是创建 Bar 对象的原因) ，spring 认识到可能发生了循环引用。

这个问题最简单的解决方案之一是在一个类和注入点上使用@Lazy注解。这指示 Spring 推迟带注解的 bean 和带注释的@Autowired 位置的初始化。这允许成功地初始化其中一个 bean，从而打破循环依赖链。理解了这一点，我们可以改变 Foo 和 Bar 类:

```java
@Component
public class Foo {

    private Bar bar;

    @Autowired
    public Foo(@Lazy Bar bar) {
        this.bar = bar;
    }
}
```

```java
@Component
@Lazy
public class Bar {

    private Foo foo;

    @Autowired
    public Bar(Foo foo) {
        this.foo = foo;
    }
}
```
