# 命令java和javac

就像开发前端项目一样，一般按照约定的目录存放项目文件，如

```sh
blog
  |- /plugin            # 用于存放项目所用到的插件
  |- /css               # 用于存放css样式文件
  |- /js                # 用于存放js脚本文件
  |- /html              # 用于存放html页面文件
  |- index.html         # 项目入口文件
```

```sh
testWebpack
  |- /dist              # 用于存放打包之后的文件
  |- /js                # 用于存放源文件
  |- index.html         # 模板文件
  |- package.json       # 定义各项重要信息
```

```sh
testJava
  |- /out                   # 用于存放编译之后的字节码文件
  |- /src                   # 用于存放源文件
    |- /com                 # 约定的com目录
      |- /companyName       # 约定的公司或者组织目录，在该文件夹下面按功能新建文件夹
        |- /utils           # 该目录下存放工具类
          |- Add.class
        |- /math            # 存放执行/验证工具类
          |- Main.class
  |- /lib                   # 存放依赖的jar包
```

# javac 命令

java入门教程都是从一个hello world例子开始的：

- 在文件夹first下新建HelloWorld.java，内容如下。
- 在文件夹first下执行`javac HelloWorld.java`等同于`javac ./HelloWorld.java`，然后会在该目录下生成HelloWorld.class文件。
- 在文件夹first下执行`java HelloWorld`，命令行（终端）会打印出Hello World!

```java
public class HelloWorld{
  public static void main(String[] args){
    System.out.println("Hello world!");
  }
}
```

经过上述步骤后的项目结构：

```sh
first
  |- HelloWorld.java                   # 源文件
  |- HelloWorld.class                  # 编译之后的字节码文件
```

javac用法：`javac <options> <source files>`

如果直接`javac <source files>`，那么会在源文件所在文件夹下生成编译后的字节码文件。如执行`javac F:/java/second/HelloWorld.java`，那么文件夹F:/java/second下会生成HelloWorld.class文件，注意即使HelloWorld.java文件头定义了包名，也是直接在文件夹F:/java/second下生成HelloWorld.class文件

如果`javac -d <directory> <source files>`，那么会在指定目录下生成类文件。如执行`javac -d F:/java/out F:/java/second/HelloWorld.java`，会在文件夹F:/java/out下生成HelloWorld.class文件，如果HelloWorld.java内容：

```java
package com.companyName.main;

public class HelloWorld{
  public static void main(String[] args){
    System.out.println("Hello world!");
  }
}
```

那么执行`javac -d F:/java/out F:/java/second/HelloWorld.java`，会在F:/java/out/com/companyName/main文件夹下生成HelloWorld.class

```sh
jar-test                    # 该文件夹在文件夹F:/java下
  |- /out                   # 用于存放编译之后的字节码文件
  |- /src                   # 用于存放源文件
    |- /com                 # 约定的com目录
      |- /companyName       # 约定的公司或者组织目录，在该文件夹下面按功能新建文件夹
        |- /utils           # 该目录下存放工具类
          |- Add.class
        |- /math            # 存放执行/验证工具类
          |- Main.class
  |- /lib                   # 存放依赖的jar包
```

Add.class

```java
package com.companyName.utils; // 表面这个类的全名是com.companyName.utils.Add

public class Add{
  public static int add(int x, int y){
    return x + y;
  }
}
```

Main.java

```java
package com.companyName.math; // 表面这个类的全名是com.companyName.math.Main

import com.companyName.utils.Add;

public class Main{
  public static void main(String[] args){
    System.out.println(Add.add(3, 6));
  }
}
```

进到文件夹jar-test下，执行`javac -d ./out ./src/com/companyName/utils/Add.java`，根据Add.class中包声明语句，会在out文件夹下嵌套生成com、companyName、utils文件夹和Add.class类文件

然后再编译Main.java文件，执行`javac -d ./out ./src/com/companyName/math/Main.java -classpath .;F:\java\jar-test\out`，注意`-classpath .;F:\java\jar-test\out`不能省，否则会报错误: 程序包com.companyName.utils不存在。因为Main.java中有包导入语句`import com.companyName.utils.Add;`，表明需要加载com.companyName.utils.Add类，`-classpath .;F:\java\jar-test\out`的作用是告诉编译器应该去哪搜索对应的Add.class文件

执行`java -classpath ./out com.companyName.math.Main`来运行Main类。

编译器和 java 虚拟机（JVM）通过将 package 名字加到 classpath 后来构造 .class 文件的路径。

一个 classpath 可能会包含好几个路径，多路径应该用分隔符分开。默认情况下，编译器和 JVM 查找当前目录。

现在我们假设classpath是`.;C:\work\project1\bin;C:\shared`，当JVM在加载`abc.xyz.Hello`这个类时，会依次查找：

- <当前目录>\abc\xyz\Hello.class

- C:\work\project1\bin\abc\xyz\Hello.class

- C:\shared\abc\xyz\Hello.class

没有设置系统环境变量，也没有传入-classpath参数，那么JVM默认的classpath为.，即当前目录：

`java abc.xyz.Hello`

上述命令告诉JVM只在当前目录搜索Hello.class。

注意在window系统中classpath中的路径以"/"分隔
