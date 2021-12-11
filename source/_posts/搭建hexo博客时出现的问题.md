---
title: 2021-12-11
date: 2021-12-11 13:40:40
tags:
---



主要是deploy部署问题的坑

1.需要输入Github账号和密码，输入之后依旧部署失败

解决方法：网上查询后得知是2021.8.13github不支持输入账号密码登陆而要求输入token登陆，在参考了这篇博客资料后成功解决了该问题，直接在需要输入密码的地方输入token。

https://blog.csdn.net/yjw123456/article/details/119696726#commentBox

2.解决上面的问题，但是出现以下错误：

```
OpenSSL SSL_read: Connection was reset, errno 10054
```

解决方法：以下这篇博客：

https://blog.csdn.net/sinat_38816924/article/details/120802126

