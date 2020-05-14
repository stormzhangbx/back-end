# criteria

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