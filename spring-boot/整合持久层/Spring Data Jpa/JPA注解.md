# JPA 注解

[Hibernate 和 JPA 注解](https：//liuyanzhao.com/7914.html)

[JPA基础（三）之注解](https：//blog.csdn.net/BIGPLANS/article/details/103251317)

## 1 @Entity

必须，将一个javabean与数据库表对应起来。

- name 可选，对应数据库中一的个表

在 Spring Boot Jpa 项目中创建一个`@Entity`修饰的pojo类，运行程序，会在数据库中创建一个对应的表，表名和列名分别由实体名和成员变量名通过驼峰自动添加下划线方式得到。

## 2 @Table

可选，通常和@Entity配合使用，只能标注在实体的class定义处,表示实体对应的数据库表的信息。

- name 可选，表示表的名称.默认地,表名和实体名称一致，只有在不一致的情况下才需要指定表名。
- catalog 可选，表示Catalog名称，默认为Catalog("")。
- schema 可选，表示Schema名称，默认为Schema("")。

## 3 @Id

必须，定义了映射到数据库表的主键的属性，一个实体只能有一个属性被映射为主键。

## 4 @GeneratedValue

可选，如果不写，主键不会自动生成，需要手动赋值。

- strategy 表示主键生成策略，值是一个枚举类型GenerationType，有AUTO，INDENTITY，SEQUENCE 和 TABLE 4种，默认为AUTO
  - AUTO 让ORM框架自动选择
  - INDENTITY 根据数据库的Identity字段生成
  - SEQUENCE 根据数据库表的Sequence字段生成
  - TABLE 根据一个额外的表生成主键

示例：

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
@Column(name = "id", nullable = false)
private Long id;
```

## 5 @Basic

可选

表示一个简单的属性到数据库表的字段的映射，对于没有任何标注的getXxxx()方法,默认即为@Basic。

- fetch 表示该属性的读取策略，有EAGER和LAZY两种，分别表示主支抓取和延迟加载,默认为EAGER

- optional 表示该属性是否允许为null，默认为true

示例：

```java
@Lob  // 大对象，映射 MySQL 的 Long Text 类型
@Basic(fetch = FetchType.LAZY) // 懒加载
@NotEmpty(message = "内容不能为空")
@Size(min = 2)
@Column(nullable = false) // 映射为字段，值不能为空
private String content;//文章全文内容
```

## 6 @column

可选，描述了数据库表中该字段的详细定义，这对于根据JPA注解生成数据库表结构的工具非常有作用。

- name 表示数据库表中该字段的名称，默认情形属性名称一致

- nullable 表示该字段是否允许为null，默认为true

- unique 表示该字段是否是唯一标识，默认为false

- length 表示该字段的大小，仅对String类型的字段有效

- insertable 表示在ORM框架执行插入操作时，该字段是否应出现INSETRT语句中,默认为true

- updateable 表示在ORM框架执行更新操作时，该字段是否应该出现在UPDATE语句中，默认为true。对于一经创建就不可以更改的字段，该属性非常有用,如对于birthday字段。

- columnDefinition 表示该字段在数据库中的实际类型。通常ORM框架可以根据属性类型自动判断数据库中字段的类型，但是对于Date类型仍无法确定数据库中字段类型究竟是DATE，TIME还是TIMESTAMPM。此外，String的默认映射类型为VARCHAR，如果要将String类型映射到特定数据库的BLOB或TEXT字段类型，该属性非常有用。

示例：

```java
@NotEmpty(message = "账号不能为空")
@Size(min=3, max=20)
@Column(nullable = false, length = 20, unique = true)
private String username; // 用户账号，用户登录时的唯一标识
```

## 7 @Transient

可选，表示该属性并非一个到数据库表的字段的映射，ORM框架将忽略该属性.如果一个属性并非数据库表的字段映射，就务必将其标示为@Transient，否则，ORM框架默认其注解为@Basic。

示例：

```java
//根据birth计算出age属性
@Transient
public int getAge() {
       return getYear(new Date()) - getYear(birth);
}
```

## 8 @ManyToOne

可选，表示 多对一 的映射，该注解标注的属性通常是数据库表的外键。

- optional 是否允许该字段为null，该属性应该根据数据库表的外键约束来确定,默认为true

- fetch 表示抓取策略，默认为FetchType.EAGER

- cascade 表示默认的级联操作策略，可以指定为ALL，PERSIST，MERGE，REFRESH和REMOVE中的若干组合(文末有详解)，默认为无级联操）

- targetEntity 表示该属性关联的实体类型。该属性通常不必指定，ORM框架根据属性类型自动判断targetEntity。

示例：

```java
@ManyToOne(cascade = {CascadeType.MERGE, CascadeType.REFRESH}, optional = false)//可选属性optional=false,表示author不能为空
@JoinColumn(name = "author_id")//设置在article表中的关联字段(外键)，author属性类型是Author，所以参考的表是author，默认参考字段是其主键
private Author author;//所属作者
```

## 9 @JoinColumn

可选，和@Column类似，但是描述的不是一个简单字段，而是一个关联字段，例如.描述一个@ManyToOne的字段。

- name 该字段的名称。由于@JoinColumn描述的是一个关联字段，如ManyToOne，则默认的名称由其关联的实体决定。

例如，实体Order有一个user属性来关联实体User，则Order的user属性为一个外键，

其默认的名称为实体User的名称+下划线+实体User的主键名称

示例： 见@ManyToOne

## 10 @OneToMany

可选，描述一个一对多的关联，该属性应该为集体类型，在数据库中并没有实际字段。

- fetch 表示抓取策略，默认为FetchType.LAZY，因为关联的多个对象通常不必从数据库预先读取到内存

- cascade 表示级联操作策略，对于OneToMany类型的关联非常重要,通常该实体更新或删除时，其关联的实体也应当被更新或删除

例如：实体 Author 和 Article 是OneToMany的关系，则实体 Author 被删除时，其关联的实体 Article也应该被全部删除

示例：

```java
@OneToMany(mappedBy = "author",cascade=CascadeType.ALL,fetch=FetchType.LAZY)
//拥有mappedBy注解的实体类为关系被维护端
 //mappedBy="author"中的author是Article中的author属性
private List<Article> articleList;//文章列表
```

## 11 @OneToOne

可选，描述一个一对一的关联

- fetch 表示抓取策略，默认为FetchType.LAZY

- cascade 表示级联操作策略

示例：

```java
@OneToOne(cascade=CascadeType.ALL)//People是关系的维护端
@JoinTable(name = "people_address",
        joinColumns = @JoinColumn(name="people_id"),
        inverseJoinColumns = @JoinColumn(name = "address_id"))//通过关联表保存一对一的关系
private Address address;//地址
```

## 12 @ManyToMany

可选, 描述一个多对多的关联。多对多关联上是两个一对多关联，但是在ManyToMany描述中，中间表是由ORM框架自动处理

- targetEntity 表示多对多关联的另一个实体类的全名，例如：package.Book.class

- mappedBy 表示多对多关联的另一个实体类的对应集合属性名称

示例：

    User实体表示用户，Book实体表示书籍，为了描述用户收藏的书籍，可以在User和Book之间建立ManyToMany关联

```java
@Entity
public class User {
   private List books;
   @ManyToMany(targetEntity=package.Book.class)
   public List getBooks() {
       return books;
   }
   public void setBooks(List books) {
       this.books=books;
   }
}
@Entity
public class Book {
   private List users;
   @ManyToMany(targetEntity=package.Users.class, mappedBy="books")
  public List getUsers() {
       return users;
   }
   public void setUsers(List users) {
       this.users=users;
   }
}
```

两个实体间相互关联的属性必须标记为@ManyToMany，并相互指定targetEntity属性,

需要注意的是，有且只有一个实体的@ManyToMany注解需要指定mappedBy属性，指向targetEntity的集合属性名称

利用ORM工具自动生成的表除了User和Book表外，还自动生成了一个User_Book表，用于实现多对多关联

## 13 @MappedSuperclass

可选, 可以将超类的JPA注解传递给子类,使子类能够继承超类的JPA注解

## 14 @Embedded

可选, @Embedded将几个字段组合成一个类，并作为整个Entity的一个属性。

例如User包括id，name，city，street，zip属性。

我们希望city，street，zip属性映射为Address对象。这样，User对象将具有id，name和address这三个属性。

Address对象必须定义为@Embededable

示例：

```java
@Embeddable
public class Address {city,street,zip}
@Entity
public class User {
   @Embedded
   public Address getAddress() {
       ..........
   }
}
```


关于级联策略这里再补充一下

- CascadeType.REMOVE

Cascade remove operation，级联删除操作。
删除当前实体时，与它有映射关系的实体也会跟着被删除。
- CascadeType.MERGE

Cascade merge operation，级联更新（合并）操作。
当Student中的数据改变，会相应地更新Course中的数据。

- CascadeType.DETACH

Cascade detach operation，级联脱管/游离操作。
如果你要删除一个实体，但是它有外键无法删除，你就需要这个级联权限了。它会撤销所有相关的外键关联。

- CascadeType.REFRESH

Cascade refresh operation，级联刷新操作。
假设场景 有一个订单,订单里面关联了许多商品，这个订单可以被很多人操作,那么这个时候A对此订单和关联的商品进行了修改,与此同时,B也进行了相同的操作,但是B先一步比A保存了数据，那么当A保存数据的时候，就需要先刷新订单信息及关联的商品信息后,再将订单及商品保存。(来自良心会痛的评论)

- CascadeType.ALL

Cascade all operations，清晰明确，拥有以上所有级联操作权限。
