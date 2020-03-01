# PreparedStatement接口继承了Statement接口

使用 Statement 需要进行拼写 SQL 语句，使用 Statement 可能会发生 SQL 注入。

调用PreparedStatement 的 setXxx(int index,Object val) 设置占位符的值. index 的值从 1 开始.

```java
String sql = "SELECT FlowID,Type,IDCard,ExamCard,StudentName,Location,Grade FROM examstudent WHERE FlowID = ?";
Connection connection = getConnection();
PreparedStatement preparedStatement = connection.prepareStatement(sql);
preparedStatement.setInt(1, 4);
```
