# 从Json静态文件中读取数据

依赖包:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.6.RELEASE</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.5</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.56</version>
    </dependency>
</dependencies>
```

Student.class:

```java
import com.alibaba.fastjson.annotation.JSONField;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private String username;

    @JSONField(deserialize = false)
    private String password;
}
```

student.json:

```json
[
  {
    "username": "qqq",
    "password": "123"
  },
  {
    "username": "www",
    "password": "456"
  }
]
```

方式一：

```java
import com.alibaba.fastjson.JSON;
import org.apache.commons.io.IOUtils;
import org.springframework.core.io.ClassPathResource;
import java.util.List;
import java.util.ArrayList;

public class School {
    public List<Student> getStudent() {
        ClassPathResource resource = new ClassPathResource("static/student.json"); // 注意这里不用加classpath
        List<Student> list = new ArrayList<>();
        if (resource.exists()) {
            try {
                list = JSON.parseArray(IOUtils.toString(resource.getInputStream(), "UTF-8"), Student.class);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return list;
    }
}
```

方式二：

```java
import com.alibaba.fastjson.JSON;
import org.apache.commons.io.IOUtils;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.Resource;
import org.springframework.stereotype.Component;

import java.io.IOException;
import java.util.List;

@Component
public class School {

    @Value("classpath:static/student.json")
    private Resource studentRes;

    public List<Student> getStudent() {
        String studentString = null;
        try {
            studentString =  IOUtils.toString(studentRes.getInputStream(), "UTF-8");
        } catch (IOException e) {
            e.printStackTrace();
        }

        List<Student> studentList = JSON.parseArray(studentString, Student.class);
        return studentList;
    }
}
```
