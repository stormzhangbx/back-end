# 返回值是具体的类还是List

有如下接口：

```java
...
public interface UserRepository extends JpaRepository<User, Long> {

}
...
```

在接口中添加一个基于方法名称来查询的方法`findByUserName`，该方法的返回值类型是`User`还是`List<User>`。

如果写成`User findByUsername(String userName);`，而实际查询结果有多个user，那么程序将报错。

如果写成`List<User> findByUsername(String userName);`，不管实际查询结果是一个user，还是有多个user，程序都正常。

如果事先知道通过`userName`查询的结果只有0条或者1条（如user表中列user_name是唯一的），那么可以将返回值写成`User`。

