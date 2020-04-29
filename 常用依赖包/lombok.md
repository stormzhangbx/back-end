# Lombok

```xml
<properties>
    <lombok.version>1.18.8</lombok.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
    </dependency>
</dependencies>
```

## @Data

会生成实例变量的无参构造函数、Getter/Setter方法、hasCode方法、toString方法、canEqual方法

## @AllArgsConstructor

生成全参构造函数

注意当同时使用@Data、@AllArgsConstructor，无参构造函数被替换成了全参构造函数，Getter/Setter方法、hasCode方法、toString方法、canEqual方法不变。此时如果依然想要无参构造函数，可以加上@NoArgsConstructor，即同时使用@NoArgsConstructor

## 遇到的坑

### JPA 实体

详情 [# JPA注意事项](../spring-boot/整合持久层/Spring%20Data%20Jpa/JPA注意事项.md)

Spring Data JPA 中如果实体类使用@Data，在双向一对一、双向一对多（多对一）、多对多中，如

```java
@Entity
@Data
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 100, nullable = false)
    private String name;

    @ManyToOne
    @JoinColumn
    private Dept dept;
}
```

```java
@Entity
@Data
public class Dept {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 100, nullable = false)
    private String name;

    @OneToMany(mappedBy = "dept")
    private List<User> userList;
}
```

若打印User对象（`System.out.println(userStr);`），会报异常：java.lang.StackOverFlowError

解决：将@Data换成@Getter和@Setter

### FastJson 反序列化

详情 [fastjson反序列化失败autoType is not support](./Fastjson/fastjson反序列化失败autoType%20is%20not%20support.md)


