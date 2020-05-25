# SHA256

```java
package com.company.hutool.crypto;

import cn.hutool.crypto.digest.DigestUtil;

public class Md5Demo {
    public static void main(String[] args) {
        String text = "123456";
        String digestHex = DigestUtil.sha256Hex(text);
        System.out.println(digestHex);
    }
}
```