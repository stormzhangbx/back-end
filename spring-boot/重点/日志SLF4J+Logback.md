# 日志SLF4J+Logback

参考：

- [SpringBoot2.0学习笔记：(四) Spring Boot的日志详解](https://blog.csdn.net/liujun03/article/details/82684209)
- [IDEA项目搭建十——使用slf4j和logback进行日志记录](https://www.cnblogs.com/taiyonghai/p/9290641.html)

SLF4j是提供了一套日志接口，Logback是接口的具体实现

日志级别从低到高：ALL、TRACE、TRACE、DEBUG、INFO、WARN、ERROR、FATAL、OFF

## 1 依赖

如果是Maven项目，需要引入如下依赖（版本号可变）

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.26</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.3</version>
</dependency>
<dependency>
    <groupId>ch.qos.logbac</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

如果是Spring boot项目，新增如下依赖即可，但是呢，实际开发中我们不需要直接添加该依赖。

```xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```


## 2 测试文件TestLog.java

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class TestLog {
    private static final Logger log = LoggerFactory.getLogger(TestLog.class); // 如果使用了lombok，可以在类上方添加@Slf4j注解简化代码
    public static void main(String[] arge){
        String msg = "I'am a debug";
        log.debug("debug: {}", msg); // {} 占位符来拼接字符串，而不需要使用 + 来连接字符串。
        log.info("info");
        log.error("error");
        log.warn("warn");
    }
}
```

## 3 配置文件

在src/main/resources目录下放入配置文件logback.xml，程序会自动查找该文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration  scan="true" scanPeriod="60 seconds" debug="false">
    <contextName>spring-boot-logging</contextName>
    <property name="log.path" value="log" />
    <!--输出到控制台-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender"> <!--name用于appender-ref标签-->
        <!-- 级别过滤 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!--输出到文件-->
    <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--定义存放日志的位置，这里使用了变量-->
        <file>${log.path}/spring-boot-logging.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志输出位置-->
            <fileNamePattern>${log.path}/spring-boot-logging.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <!-- 日志保存周期 -->
            <maxHistory>30</maxHistory>
            <!-- 总大小 -->
            <totalSizeCap>1GB</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="console" />
        <appender-ref ref="file" />
    </root>

    <!-- logback为java中的包 -->
    <logger name="com.baiding"/>

</configuration>
```

### 3.1 configuration

根节点，支持如下属性

- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true
- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟
- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false

### 3.2 property

用来定义变量值的标签，有两个属性，name和value；其中name的值是变量的名称，value的值时变量定义的值。定义变量后，可以使`${}`来使用变量

### 3.3 appender

用于定义appender，有俩个属性name和class，class用来指定哪种输出策略，常用就是控制台输出策略和文件输出策略。通过不同的appender把同一条日志输出到不同的目的地。

- console: 输出到屏幕
- file：输出到文件
- socket：通过网络输出到远程计算机
- jdbc：输出到数据库

#### 3.3.1 file

file属性定义文件的带全路径的文件名

#### 3.3.2 rollingPolicy

常见的日志输出到文件，随着应用的运行时间越来越长，日志也会增长的越来越多，将他们输出到同一个文件并非一个好办法。

`<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">`是最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责触发滚动。

##### 3.3.2.1 fileNamePattern

定义了日志的切分方式——把每一天的日志归档到一个文件中，同理，可以使用%d{yyyy-MM-dd HH-mm}来定义精确到分的日志切分方式。

##### 3.3.2.2 maxHistory

表示只保留最近30天的日志

##### 3.3.2.3 totalSizeCap

用来指定日志文件的上限大小，例如设置为1GB的话，那么到了这个值，就会删除旧的日志

#### 3.3.3 encoder

对日志进行格式化

- %d{HH:mm:ss.SSS} ：日志的输出时间
- %contextName ： 上下文名称
- %thread : 输出日志的进程名字，这在Web应用以及异步任务处理中很有用
- %-5level : 日志级别，并且使用5个字符靠左对齐
- %logger{36} : 日志输出者的名字(一般为类名),名字最长36个字符，否则按照句点分割
- %msg ： 具体的日志消息
- %n ：换行符

### 3.3.4 root

`<root level="info">`表示只输出info级别即以上的日志

将上述定义的appender放入appender-ref中

### 3.3.5 logger

用来设置某一个包或者具体的某一个类的日志打印级别、以及指定appender
