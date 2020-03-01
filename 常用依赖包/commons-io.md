# commons-io

```xml
<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.5</version>
</dependency>
```

## org.apache.commons.io.IOUtils

- closeQuietly：关闭一个IO流、socket、或者selector且不抛出异常，通常放在finally块
- toString：转换IO流、 Uri、 byte[]为String
- copy：IO流数据复制，从输入流写到输出流中，最大支持2GB
- toByteArray：从输入流、URI获取byte[]
- write：把字节. 字符等写入输出流
- toInputStream：把字符转换为输入流
- readLines：从输入流中读取多行数据，返回List<String>
- copyLarge：同copy，支持2GB以上数据的复制
- lineIterator：从输入流返回一个迭代器，根据参数要求读取的数据量，全部读取，如果数据不够，则失败

## org.apache.commons.io.FileUtils

- deleteDirectory：删除文件夹
- readFileToString：以字符形式读取文件内容
- deleteQueitly：删除文件或文件夹且不会抛出异常
- copyFile：复制文件
- writeStringToFile：把字符写到目标文件，如果文件不存在，则创建
- forceMkdir：强制创建文件夹，如果该文件夹父级目录不存在，则创建父级
- write：把字符写到指定文件中
- listFiles：列举某个目录下的文件(根据过滤器)
- copyDirectory：复制文件夹
- forceDelete：强制删除文件
