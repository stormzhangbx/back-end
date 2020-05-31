# 获取Class类型对象的三种方式

## 1 类名.class

运用.class的方式来获取Class实例。

```java
Class c1 = Animal.class;
```

## 2 实例名.getClass()

任何类都继承自Object类，在Object类中定义了getClass方法，因此任何实例对象都有这个方法。

```java
Animal animal = new Animal();
Class c2 = animal.getClass();
```

## 3 Class.forName("XXX")

使用Class类的静态方法forName，参数为类的全限定名，这种方式灵活性最高，根据类的字符串全名即可获取Class实例，可以动态加载类，框架设计经常用到。

```java
Class c3 = null;
try {
    c3 = Class.forName("com.company.Animal");
} catch (ClassNotFoundException e) {
    e.printStackTrace();
}
```