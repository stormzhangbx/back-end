# criteria

[https://www.objectdb.com/java/jpa/query](https://www.objectdb.com/java/jpa/query)

Criteria 和 JPQL 是类似的，只不过前者通过面向对象的方式来进行sql操作。

JPQL

```java
//创建一个TypedQuery实例
TypedQuery<Country> query = em.createQuery("SELECT c FROM Country c", Country.class);
//执行SELECT查询，并返回查询结果
List<Country> resluts = query.getResultList();
```

Criteria

```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Country> q = cb.createQuery(Country.class);
Root<Country> c = q.from(Country.class);
q.select(c);

TypedQuery<Country> query = em.createQuery(q);
List<Country> results = query.getResultList();
```