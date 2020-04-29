# Fastjson

[全解史上最快的JSON解析库 - alibaba Fastjson](https://www.cnblogs.com/jajian/p/10051901.html)

[github](https://github.com/alibaba/fastjson)

```xml
<properties>
    <fastjson.version>1.2.61</fastjson.version>
</properties>
<dependencies>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>${fastjson.version}</version>
    </dependency>
</dependencies>
```

## 1 JSONObject 和 JSONArray

[JSONObject和JSONArray区别及基本用法](https://www.cnblogs.com/QW-lzm/p/10703618.html)

```java
public static final Object toJSON(Object javaObject); //将JavaBean转换为JSONObject或者JSONArray。
```
