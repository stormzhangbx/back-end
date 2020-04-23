# JPA 注解

[Hibernate 和 JPA 注解](https://liuyanzhao.com/7914.html)
[JPA基础（三）之注解](https://blog.csdn.net/BIGPLANS/article/details/103251317)

在 Spring Boot Jpa 项目中创建一个`@Entity`修饰的pojo类，运行程序，会在数据库中创建一个对应的表，表名和列名分别由实体名和成员变量名通过驼峰自动添加下划线方式得到。

如果想更改驼峰自动添加下划线方式，修改Spring Boot配置文件

```
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```

@Table

可以重新设置实体对应的表名。

@column

可以重新设置成员变量名对应的列名。
