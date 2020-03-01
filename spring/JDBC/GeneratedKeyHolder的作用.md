# GeneratedKeyHolder的作用

Spring利用GeneratedKeyHolder，提供了一个可以返回新增记录对应主键值的方法： 

`int update(PreparedStatementCreator psc, KeyHolder generatedKeyHolder)`

Spring为KeyHolder接口指代了一个通用的实现类GeneratedKeyHolder，该类返回新增记录时的自增长主键值。
