# commons-lang3

[官网](http://commons.apache.org/proper/commons-lang/)

```xml
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-lang3</artifactId>
  <version>3.9</version>
</dependency>
```


## org.apache.commons.lang3.StringUtils

- hasText：检查字符串中是否包含文本
- hasLength：检测字符串是否长度大于0
- isEmpty：检测字符串是否为空（若传入为对象，则判断对象是否为null）
- commaDelimitedStringToArray：逗号分隔的String转换为数组
- collectionToDelimitedString：把集合转为CSV格式字符串
- replace 替换字符串
- delimitedListToStringArray：相当于split
- uncapitalize：首字母小写
- collectionToDelimitedCommaString：把集合转为CSV格式字符串
- tokenizeToStringArray：和split基本一样，但能自动去掉空白的单词

## org.apache.commons.lang3.ArrayUtils

- contains：是否包含某个字符串
- addAll：添加整个数组
- clone：克隆一个数组
- isEmpty：是否空数组
- add：向数组添加元素
- subarray：截取数组
- indexOf：查找某个元素的下标
- isEquals：比较数组是否相等
- toObject：基础类型数据数组转换为对应的Object数组
