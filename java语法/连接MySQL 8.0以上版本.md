# Java连接MySQL数据库8.0以上版本遇到的坑（The new driver class is `com.mysql.cj.jdbc.Driver）

mysql8.0和之前版本的区别，首先驱动换了，不是com.mysql.jdbc.Driver而是'com.mysql.cj.jdbc.Driver',此外mysql8.0是不需要建立ssl连接的，你需要显示关闭。最后你需要设置serverTimezone

```java
// 加载驱动类
// static final String URL = "jdbc:mysql://localhost:3306/test?useSSL=false";
String URL = "jdbc:mysql://localhost:3306/test?serverTimezone=Asia/Shanghai&characterEncoding=utf8&useSSL=false";
// 建立连接
// String path = "com.mysql.jdbc.Driver";
String path = "com.mysql.cj.jdbc.Driver";
```
