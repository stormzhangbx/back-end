# JPA注意事项

## 1 修改默认的驼峰转下划线方式

如果想更改驼峰自动添加下划线方式，修改Spring Boot配置文件。

```
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```

## 2 @Data的坑

不要为了省事，直接在实体类上使用@Data注解，在关联查询（一对一、一对多、多对多）中，一个实体类的成员变量可能是另一个实体类（或包含实体类的List、Set），使用@Data生成的toString方法会打印类中所有的成员变量，此时会报错：java.lang.StackOverFlowError，或者在接口中返回该实体实例时，也会报这个错误。

例如：

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

简单的测试：

```java
public static void main(String[] args) {
    Dept dept = new Dept();
    dept.setName("developer");

    User user = new User();
    user.setName("Jack");
    user.setDept(dept);

    List<User> userList = new ArrayList<>();
    userList.add(user);
    dept.setUserList(userList);

    System.out.println(user); //会报java.lang.StackOverflowError错误
}
```
原因：

上述打印会陷入user -> dept -> user -> dept...的死循环中。
解决办法：

使用@Getter、@Setter，而不使用@Data。如果需要可以通过IDEA快捷方式快速生成toString、equals和hasCode方法。
