# Comparable 和 Comparator 比较

[Java 中 Comparable 和 Comparator 比较](https://www.cnblogs.com/skywang12345/p/3324788.html)

- Collections.sort 用于对List进行排序，可以改变原List中元素顺序
  - `Collections.sort(List<T>): void`
  - `Collections.sort(List<T>, Comparator<? super T>): void`
  
## Comparable接口

```java
package java.lang;
import java.util.*;

public interface Comparable<T> {
  public int compareTo(T o);
}
```

Comparable接口的实现类必须重写comparaTo方法。

Number、String等对象都有compareTo方法，查看源码可以发现都实现了Comparable接口。实现Comparable接口的类的对象之间可以通过compareTo进行比较，

```java
public class Person implements Comparable<Person> {
    public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    private int age;

    private String name;

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    @Override
    public int compareTo(Person o) {
        return name.compareTo(o.name);
    }

    // 省略了get、set方法

    public static void main(String[] args) {
        //我要比较两个人p1、p2，比较他们的什么呢？比较他们的姓名
        //实现了Comparable接口的类的对象可以使用comparaTo进行比较
        Person p1 = new Person(12, "Jack");
        Person p2 = new Person(30, "Marry");
        //System.out.println(p1.compareTo(p2)); //-3
        System.out.println(p2.compareTo(p1)); //3

        List<Person> personList = new ArrayList<>();
        personList.add(new Person(20, "ccc"));
        personList.add(new Person(30, "AAA"));
        personList.add(new Person(10, "bbb"));
        personList.add(new Person(40, "ddd"));

        //System.out.println(personList); //[Person{age=20, name='ccc'}, Person{age=30, name='AAA'}, Person{age=10, name='bbb'}, Person{age=40, name='ddd'}]

        //既可以直接排序，也可以使用比较器进行排序
        //直接排序
        Collections.sort(personList);
        System.out.println(personList); //[Person{age=30, name='AAA'}, Person{age=10, name='bbb'}, Person{age=20, name='ccc'}, Person{age=40, name='ddd'}]

        //通过比较器对personList进行排序
        Collections.sort(personList, (o1, o2) -> o1.age - o2.age);
        System.out.println(personList); //[Person{age=10, name='bbb'}, Person{age=20, name='ccc'}, Person{age=30, name='AAA'}, Person{age=40, name='ddd'}]
    }
}
```

## Comparator接口

```java
@FunctionalInterface
public interface Comparator<T> {
  int compare(T o1, T o2);

  boolean equals(Object obj);

  //其他静态、默认方法
}
```

Comparator接口的实现类必须重写compare方法，可以不重新equals方法，因为一切类都继承自java.lang.Object，也就继承了Object类的equals方法。

Comparator接口的实现类被称作是比较器。

```java
public class People{
    private int age;

    private String name;

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public People(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public static void main(String[] args) {
        //因为People没有实现Comparable接口，这里只能使用比较器进行排序
        List<People> peopleList = new ArrayList<>();
        peopleList.add(new People(12, "Marry"));
        peopleList.add(new People(30, "Jack"));

        //Collections.sort(peopleList); // 报错

        //Collections.sort(peopleList, new Comparator<People>() {
        //    @Override
        //    public int compare(People o1, People o2) {
        //        return o1.name.compareTo(o2.name);
        //    }
        //});

        Collections.sort(peopleList, (o1, o2) -> o1.name.compareTo(o2.name));

        System.out.println(peopleList);


    }
}
```