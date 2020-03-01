# fastjson

[参考](https://www.cnblogs.com/jajian/p/10051901.html)

## 转换场景

[github](https://github.com/alibaba/fastjson)

json字符串的含义：首先记住是个字符串，只是格式是json.

有哪些转换场景：

javabean对象 转换成 json字符串`"{}"`

Map 转换成 json字符串`"{}"`

List  转换成 json字符串`"[]"`

json字符串`"{}"`

## Fastjson 主要的API

以下方法都是JSON类下的静态方法：

1. 把JSON文本parse为JSONObject或者JSONArray

```java
public static final Object parse(String text);
```

```java
JSONObject o = (JSONObject)JSON.parse(stringData)
```

2. 解析{}类型json字符串

parseObject方法可以理解成把字符串解析成{}型的对象

```java
public static final JSONObject parseObject(String text)； // 2.1 把JSON文本parse成JSONObject
public static final <T> T parseObject(String text, Class<T> clazz); // 2.2 把JSON文本parse为JavaBean对象
```

3. 解析[]类型json字符串

parseArray方法可以理解成把字符串解析成[]型是对象

```java
public static final JSONArray parseArray(String text); // 3.1 把JSON文本parse成JSONArray
public static final <T> List<T> parseArray(String text, Class<T> clazz); //把JSON文本parse成JavaBean集合，即List
```

可以通过如下方法获取JSONArray对象中的数据

```java
JSONObjdect jsonObject = (JSONObjdect) jsonArray.get(0)
int id = jsonObject.getInteger("id")
```

4. 将对象、List、Map序列化为json字符串

```java
public static final String toJSONString(Object object); // 将JavaBean序列化为JSON文本
public static final String toJSONString(Object object, boolean prettyFormat); // 将JavaBean序列化为带格式的JSON文本
public static final Object toJSON(Object javaObject); //将JavaBean转换为JSONObject或者JSONArray。
```

## JSONObject 和 JSONArray

[JSONObject和JSONArray区别及基本用法](https://www.cnblogs.com/QW-lzm/p/10703618.html)
