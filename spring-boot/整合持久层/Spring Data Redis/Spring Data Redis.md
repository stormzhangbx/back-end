# Spring Data Redis

属性：

`org.springframework.boot.autoconfigure.data.redis.RedisProperties`


自动配置：

`org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration`



`RedisTemplate` 和 `StringRedisTemplate` 区别

自定义（反）序列化需要实现 `RedisSerializer` 接口，默认（反）序列化是 `JdkSerializationRedisSerializer`
