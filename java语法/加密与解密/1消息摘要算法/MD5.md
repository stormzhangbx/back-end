# MD5

[摘要加密-Digester和HMac](https://www.hutool.cn/docs/#/crypto/%E6%91%98%E8%A6%81%E5%8A%A0%E5%AF%86-Digester%E5%92%8CHMac)

```java
package com.company.hutool.crypto;

import cn.hutool.crypto.digest.DigestUtil;

public class Md5Demo {
    public static void main(String[] args) {
        String text = "123456";
        String digestHex = DigestUtil.md5Hex(text); //得到十六进制密文
        System.out.println(digestHex); //e10adc3949ba59abbe56e057f20f883e
    }
}
```