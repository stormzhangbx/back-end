# fastjson反序列化失败autoType is not support

[fastjson反序列化失败autoType is not support](https://blog.csdn.net/sz85850597/article/details/85695418)

```java
@Data
@AllArgsConstructor
public class User {
    @JSONField(name = "name")
    private String username;
    @JSONField(serialize = false, ordinal = 1)
    private String password;
    @JSONField(ordinal = 2)
    private int age;
    @JSONField(ordinal = 3, format = "yyyy-MM-dd HH:mm:ss")
    private Date birthday;
    @JSONField(ordinal = 3)
    private Dept dept;
}
```

```java
@Data
@AllArgsConstructor
public class Dept {
    private Long id;
    private String name;
}
```

```java
Dept dept = new Dept(1L, "developer");
Date date = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").parse("2020-10-25 08:50:00");
User user = new User("Jack", "123456", 20, date, dept);
String userStr = JSON.toJSONString(user, SerializerFeature.WriteClassName); //序列化时，使用WriteClassName的特性
System.out.println(userStr); //{"@type":"com.company.fastjson.User","name":"Jack","age":20,"birthday":"2020-10-25 08:50:00","dept":{"id":1,"name":"developer"}}
```


```java
ParserConfig.getGlobalInstance().setAutoTypeSupport(true);
String str = "{\"@type\":\"com.company.fastjson.User\",\"name\":\"Jack\",\"age\":20,\"birthday\":\"2020-10-25 08:50:00\",\"dept\":{\"id\":1,\"name\":\"developer\"}}";
Object o = JSON.parseObject(str, Object.class);
System.out.println(o);
```
此时会报异常：com.alibaba.fastjson.JSONException: autoType is not support. com.company.fastjson.User

原因：因为该javabean（User类）没有无参构造方法，被fastjson判断为接口或抽象类，最终抛出autoType is not support的异常。

解决：给javabean添加无参构造方法。

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    @JSONField(name = "name")
    private String username;
    @JSONField(serialize = false, ordinal = 1)
    private String password;
    @JSONField(ordinal = 2)
    private int age;
    @JSONField(ordinal = 3, format = "yyyy-MM-dd HH:mm:ss")
    private Date birthday;
    @JSONField(ordinal = 3)
    private Dept dept;
}
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Dept {
    private Long id;
    private String name;
}
```
