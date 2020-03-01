# 内存

[Java 内存分配全面浅析](https://blog.csdn.net/shimiso/article/details/8595564)

- 栈：保存局部变量的值，包括：1.用来保存基本数据类型的值；2.保存类的实例，即堆区对象的引用(指针)。
- 堆：用来存放动态产生的值，比如new出来的对象。
- 常量池：JVM为每个已加载的类型维护一个常量池，常量池就是这个类型用到的常量的一个有序集合。基本类型的包装类分别是：Byte、Short、Character、Integer、Long、Boolean，都实现了常量池技术，另外两种浮点数类型的包装类则没有实现。另外，String类型也实现了常量池技术。

```java
package com.company;

import com.company.pojo.User;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class StringTest {

    @Test
    public void testBytes() {
        //测试一
        int data = 100;
        System.out.println(data); //100
        change(data);
        System.out.println(data); //100

        //测试二
        User user = new User("Jack", "123456");
        System.out.println(user.getPassword()); //123456
        change1(user);
        System.out.println(user.getPassword()); //456789

        //测试三
        int a = 100;
        int b = 100;
        System.out.println(a == b); //true
        Integer m = 100;
        Integer n = 100;
        System.out.println(m == n); //true
        Integer m1 = 128;
        Integer n1 = 128;
        System.out.println(m1 == n1); //false
    }

    public void change(int d) {
        d = 200;
    }

    public void change1(User u) {
        u.setPassword("456789");
    }
}
```

测试一：

- 创建一个int型的变量data，由于是基本类型，直接在栈中存放data对应的值100。所以接下来打印100
- 接着调用change方法，d是局部变量，因此会把d放到栈中，并直接把data的值（100）赋值给d。
- 把200赋值给d。change执行完毕，立即释放局部变量d所占用的空间。
- data的并没有改变，任然打印100

测试二：

- `User user = new User("Jack", "123456");` 理解成 `User user; user = new User("Jack", "123456")`。user在栈中，存的是`new User("Jack", "123456")`在堆中的地址。接下来打印123456
- JVM会在栈中为局部引用变量u分配空间，并且把user中的指针存放在u中，此时user和u指向同一个对象。再调用实例u的setPassword方法，其实就是调用user指向的对象的setPassword方法。

测试三：

- a和b均是普通类型(int)的变量，所以数据直接存储在栈中，而栈有一个很重要的特性：栈中的数据可以共享。当我们定义了int a = 100;，再定义int b = 100;这时候会自动检查栈中是否有100这个数据，如果有，b会直接指向a的40，不会再添加一个新的100。

- 因为Integer是包装类，m和n均是引用类型，在栈中存储指针。由于Integer包装类实现了常量池技术，因此m、n的100均是从常量池中获取的，均指向同一个地址，因此m=n。

- 以上提到的几种基本类型包装类均实现了常量池技术，但他们维护的常量仅仅是【-128至127】这个范围内的常量，如果常量值超过这个范围，就会从堆中创建对象，不再从常量池中取。很明显超过了127，无法从常量池获取常量，就要从堆中new新的Integer对象，这时m1和n1就不相等了。
