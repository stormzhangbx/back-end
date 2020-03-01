# 加密

## 1 通用散列算法

常见的散列算法有MD5、SHA。如果直接对密码进行散列，容易破解，如可以在一些md5解密网站很容易通过散列值得到源密码。若在散列时加一些只有系统知道的干扰数据（即盐），这样生成的散列值更难被破解

```java
import org.apache.shiro.crypto.hash.SimpleHash;
...
String algorithmName = "SHA-1" // 散列算法名称
String password = "123456"; // 源
String salt = "123"; // 盐
int hashIterations = "3" // 散列次数
//内部使用MessageDigest
String simpleHash = new SimpleHash(algorithmName, password, salt, hashIterations).toString();
```

可以通过如下方式生成盐

```java
import org.apache.shiro.crypto.SecureRandomNumberGenerator;
...
String salt = new SecureRandomNumberGenerator().nextBytes().toString();
```

## 2 具体散列算法

```java
import org.apache.shiro.crypto.hash.Md5Hash;
...
String password = "123456";
String salt = "123";
int hashIterations = 3;
// String result1 = new Md5Hash(password).toString(); // 还可以转换为 toBase64()/toHex()
// String result2 = new Md5Hash(password, salt).toString();
String result3 = new Md5Hash(password, salt, hashIterations).toString();
```

通过查看源码可知，具体散列算法内部其实使用的通用散列算法simpleHash


对盐和加密次数理解

md5加密又有一些缺陷:
1. 如果我的密码是 123,你的也是 123, 那么md5的值是一样的，那么通过比较加密后的字符串，我就可以反推过来，原来你的密码也是123.
2. 与上述相同，虽然 md5 不可逆，但是我可以穷举法呀，我把特别常用的100万或者更多个密码的 md5 值记录下来，比如12345的，abcde的。 相当一部分人用的密码也是这些，那么只要到数据库里一找，也很快就可以知道原密码是多少了。这样看上去也就破解了，至少一部分没有想象中那么安全吧。
为了解决这个问题，引入了盐的概念。 盐是什么意思呢？ 比如炒菜，直接使用md5，就是对食材（源密码）进行炒菜，因为食材是一样的，所以炒出来的味道都一样，可是如果加了不同分量的盐，那么即便食材一样，炒出来的味道也就不一样了。

所以，虽然每次 123 md5 之后都是202CB962AC59075B964B07152D234B70，但是 我加上盐，即 123+随机数，那么md5值不就不一样了吗？ 这个随机数，就是盐，而这个随机数也会在数据库里保存下来，每个不同的用户，随机数也是不一样的。
再就是加密次数，加密一次是202CB962AC59075B964B07152D234B70，我可以加密两次呀，就是另一个数了。 而黑客即便是拿到了加密后的密码，如果不知道到底加密了多少次，也是很难办的。
