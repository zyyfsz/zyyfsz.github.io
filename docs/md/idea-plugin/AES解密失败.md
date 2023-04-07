# AES 解密报错 Illegal key size

AES 加密使用的是 256位，Java 默认使用的解密包是`local_policy.jar`和`US_export_policy.jar`，但是这个默认的只支持 128位的解密(java 版本在`1.8.0_161`之后就没有这个问题了，默认是支持)。
我们的版本是`1.8.0_151`正好默认是只支持 128位的解密(其实不是不支持，只是默认配置的不支持)。

## 解决办法
在前面我们没有提及一个东西，就是在`/usr/local/java/jdk1.8.0_151/jre/lib/security/policy/`下有两个目录

```code
[root@djx-117106 policy]# pwd
/usr/local/java/jdk1.8.0_151/jre/lib/security/policy/
[root@djx-117106 policy]# ls -l
total 8
drwxr-xr-x 2 root root 4096 Nov  2 10:47 limited
drwxr-xr-x 2 root root 4096 Nov  2 10:47 unlimited
[root@djx-117106 policy]# ls -l ./limited/
total 8
-rw-r--r-- 1 root root 3405 Jul  4 19:41 local_policy.jar
-rw-r--r-- 1 root root 2920 Jul  4 19:41 US_export_policy.jar
[root@djx-117106 policy]# ls -l ./unlimited/
total 8
-rw-r--r-- 1 root root 2929 Jul  4 19:41 local_policy.jar
-rw-r--r-- 1 root root 2917 Jul  4 19:41 US_export_policy.jar
```
有一个`limited`目录(也就是对解密有限制的包，只支持 128位)，也有一个`ulimited`目录(也就是没有限制的目录)。

### 1、更改源码
我们在`/usr/local/java/jdk1.8.0_151/jre/lib/security/`下的`java.security`文件中看到。

```code
# To support older JDK Update releases, the crypto.policy property
# is not defined by default. When the property is not defined, an
# update release binary aware of the new property will use the following
# logic to decide what crypto policy files get used :
#
# * If the US_export_policy.jar and local_policy.jar files are located
# in the (legacy) <java-home>/lib/security directory, then the rules
# embedded in those jar files will be used. This helps preserve compatibility
# for users upgrading from an older installation.
#
# * If crypto.policy is not defined and no such jar files are present in
# the legacy locations, then the JDK will use the limited settings
# (equivalent to crypto.policy=limited)
#
# Please see the JCA documentation for additional information on these
# files and formats.
#crypto.policy=unlimited
```
注意下文中的`(equivalent to crypto.policy=limited)`说明默认是使用的`limited`.
我们只需要加`crypto.policy=unlimited`. 让默认使用的不限制的。

### 2、替换Jar包
**替换`/usr/local/java/jdk1.8.0_151/jre/lib/security/policy/limited`的路径的包。**
我们可以直接用`/usr/local/java/jdk1.8.0_151/jre/lib/security/policy/unlimited`下面的包
直接替换`/usr/local/java/jdk1.8.0_151/jre/lib/security/policy/limited/`下面的两个包,也就是让默认使用不限制的jar包。

### 3、升级 Java 版本
https://www.oracle.com/technetwork/java/javase/8u161-relnotes-4021379.html

就是从`1.8.0_161-b12`版本后，默认将采用无限制的加密算法，也就是使用`unlimited`下的jar包。我们也可以通过设置`java.security`文件的`crypto.policy`的值来改变这个默认的值。
