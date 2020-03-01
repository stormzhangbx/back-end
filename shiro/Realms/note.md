# Credentials Matching 凭证匹配

用户密码是明文的，这样是有巨大风险的，一旦泄露，就不好了。所以，通常都会采用非对称加密，什么是非对称呢？就是不可逆的，而 md5 就是这样一个算法.如代码所示 123 用 md5 加密后，得到字符串： 202CB962AC59075B964B07152D234B70，这个字符串，却无法通过计算，反过来得到源密码是 123.这个加密后的字符串就存在数据库里了，下次用户再登陆，输入密码 123， 同样用md5 加密后，再和这个字符串一比较，就知道密码是否正确了。如此这样，既能保证用户密码校验的功能，又能保证不暴露密码。

Credentials Matching 即用于验证用户输入的用户名和密码是否正确

Realm默认使用SimpleCredentialsMatcher，SimpleCredentialsMatcher使用AuthenticationToken中提交的内容对存储的帐户凭据执行简单的直接相等性检查。

通常更多使用HashedCredentialsMatcher

1. CodeAndCrypto.java，用于生成MyRealm用到的salt和加密后的密码encodePassword

```java
package com.company.shiro.chapter5;

import org.apache.shiro.crypto.SecureRandomNumberGenerator;
import org.apache.shiro.crypto.hash.SimpleHash;
import org.junit.Test;

public class CodeAndCryptoTest {
    @Test
    public void testSimpleHash() {
        String password = "123456";
        // 这里的salt可以自己指定，也可以通过下述方式随机生成
        String salt = new SecureRandomNumberGenerator().nextBytes().toString(); // 2GJwiZj2TE5BrVTsOp0jTA==
        int times = 3;
        String algorithmName = "md5";
        String encodePassword = new SimpleHash(algorithmName, password, salt, times).toString(); // 2cc8003db6abb4b6ab3c9060fa378b23
    }
}
```

2. MyRealm.java

```java
package com.company.shiro.chapter5.realm;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.ByteSource;

public class MyRealm2 extends AuthorizingRealm {

    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        return null;
    }

    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        String username= token.getPrincipal().toString();
        // 实际工作中salt、password是在数据库中通过username查询得到的（这些信息是在创建账户时保存的）
        String salt = "2GJwiZj2TE5BrVTsOp0jTA=="; // 通过CodeAndCryptoTest中的testSimpleHash生成
        String password = "2cc8003db6abb4b6ab3c9060fa378b23"; // 通过CodeAndCryptoTest中的testSimpleHash生成
        SimpleAuthenticationInfo a = new SimpleAuthenticationInfo(username, password, ByteSource.Util.bytes(salt), getName());
        return a;
    }
}
```

3. shiro-hashedCredentialsMatcher.ini，修改Realm默认的凭证匹配

```ini
[main]
credentialsMatcher=org.apache.shiro.authc.credential.HashedCredentialsMatcher
credentialsMatcher.hashAlgorithmName=md5
credentialsMatcher.hashIterations=3

myRealm=com.company.shiro.chapter5.realm.MyRealm2
myRealm.credentialsMatcher=$credentialsMatcher
securityManager.realms=$myRealm
```

4. PasswordTest.java，最终测试方法

```java
package com.company.shiro.chapter5;

import org.junit.Test;

public class PasswordTest extends BaseTest{

    @Test
    public void testHashedCredentialsMatcherWithMyRealm2() {
        login("classpath:shiro-hashedCredentialsMatcher.ini", "liu", "123456");
    }
}
```
