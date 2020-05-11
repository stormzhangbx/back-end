# List

[Java 集合系列03之 ArrayList详细介绍(源码解析)和使用示例](https://www.cnblogs.com/skywang12345/p/3308556.html)

## List 转数组

```java
List<String> list = new ArrayList<>();
list.add("Jack");
list.add("Tom");
list.add("Marry");

//Object[] objects = list.toArray();

String[] strings = list.toArray(new String[0]);
```