# spring-boot-devtools

## 1 安装依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

## 2 devtools的常用作用

### 2.1 设置一些默认值

将一些依赖的缓存功能设置为false，如

```properties
spring.thymeleaf.cache=false
```

模板引擎 thymeleaf 缓存功能默认是开启的，这在生产环境是有用的，因为生产环境模板文件是不会改变的，缓存功能可以避免重复的解析模板文件，从而提高效率。而开发环境模板文件可能经常变化，如果使用缓存功能，当修改模板文件后，重新构建项目，浏览器上看到的还是修改之前的模板页面。

### 2.2 自动restart

当classpath下的文件改变时程序会自动restart，/META-INF/maven, /META-INF/resources, /resources, /static, /public, 或 /templates虽然在classpath下，但其中文件变化不会触发自动restart。

如果使用Eclipse，保存一个修改的文件会更新classpath，从而触发restart；如果使用IDEA，**构建项目（Build Project）** 会更新classpath，从而触发restart。自动构建也就意味者IDEA需要消耗更多的资源去实时监测代码改动，性能也会受到影响，而且修改代码后有时候要很长时间才自动构建，有时候压根就不自动构建，还不如手动构建来的快。所以使用IDEA，不建议设置自动构建项目。

改动源文件后，再构建项目（快捷键`Ctrl + F9`，或直接点击IDEA上的Build Project按钮）。

| 改动源文件类型     | 构建后target目录是否产生相应的改动 | 是否触发restart | 接口返回结果是否发生相应改动 |
| :---------------- | :--------------------------------- | :-------------- | :--------------------------- |
| 修改Java文件      | 是                                 | 是              | 是                           |
| 新增/删除Java文件 | 是                                 | 是              | 是                           |
| 修改静态文件      | 是                                 | 否              | 是                           |
| 新增/删除静态文件 | 否                                 | 否              | 否                           |

注意：target目录是否产生相应的改动，这跟是否使用spring-boot-devtools无关
