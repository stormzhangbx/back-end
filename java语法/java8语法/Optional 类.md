# Optional 类

[深入理解java8中的Optional 类](https://blog.csdn.net/xhd731568849/article/details/79532959)

可以将Optional实例理解成一个容器（所以该类是一个泛型类）。

## 1 创建 Optional  实例

Optional类没有public的构造函数，创建Optional实例，只能通过下面3个Optional类的静态方法。

- `empty(): Optional<T>` 返回一个空的Optional实例。

```java
Optional<User> emptyOpt = Optional.empty();
```

- `of(T): Optional<T>` 返回一个具有非null值的Optional实例，如果把 null 值作为参数传递进去，会抛出 NullPointerException。

```java
User user = new User();
user.setName("Jack");
Optional<User> opt = Optional.of(user); //传入方法of中的参数不能为null，否则抛出NullPointerException
```

- `ofNullable(T): Optional<T>` 如果传入的参数非null，返回一个描述具体值的Optional实例，否则返回一个空的Optional实例。

```java
User user = null;
Optional<User> opt = Optional.ofNullable(user); //传入方法of中的参数可以为null
```

## 2 访问 Optional 实例的值

上面介绍了如何创建Optional实例，开始说过可以将Optional实例理解成一个容器，现在介绍如何从Optional这个实例容器中取值。

- `get(): T` 如果存在值就返回值，否则抛出NoSuchElementException。
- `orElse(T): T` 如果存在值就返回值，否则就返回调用该方法时传入的参数值。这个方法类似。Python中字典的get方法（`radiansdict.get(key, default=None)` 返回指定键的值，如果值不在字典中返回default值）。
- `orElseGet(Supplier<? extends T): T` 如果存在值就返回值，否则执行作为参数传入的 Supplier(供应者) 函数式接口，并将返回其执行结果。




