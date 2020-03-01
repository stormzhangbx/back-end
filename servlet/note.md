去官网下载 64-bit Windows zip (pgp, sha512)，适用于64位window系统

报错：java.lang.UnsatisfiedLinkError: Can't load AMD 64-bit .dll on a IA 32-bit' platform

今天使用idea启动项目的时候报了java.lang.UnsatisfiedLinkError这个错误，查阅资料后知道是jdk和tomcat冲突了，tomcat是64位的，而jdk是32位的，所以报错了。

提示:如果不知道自己的jdk是32位还是64位,可以使用一下方法:

打开cmd,并在cmd中输入:java -version

查看第三行内容是否包含“64”，如果不包含，说明你设置的【JAVA_HOME】的地址指向的是32bit的JDK，那么你就需要下载一个64bit的JDK安装，并修改【JAVA_HOME】指向64bit的文件。

重新下载安装jdk后可以重新使用java -version来验证一下是否安装完成
