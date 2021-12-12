---
title: 使用hexo搭建博客时遇到的问题
date: 2021-12-11 13:40:40
tags: [hexo, 学习]
categories:
- 博客
---

主要是deploy部署问题的坑

### 1.需要输入Github账号和密码，输入之后依旧部署失败

问题描述：在输入命令 `hexo d` 来部署时提示需要输入Github的账号和密码进行验证，输入后提示登陆验证失败。

解决方法：网上查询后得知是2021年8月13日之后github不支持输入账号密码登陆而要求输入token登陆，在参考了这篇博客资料后成功解决了该问题，直接在需要输入密码的地方输入token。

https://blog.csdn.net/yjw123456/article/details/119696726#commentBox

### 2.解决上面的问题，但是出现openSSL问题

问题描述：错误信息：`OpenSSL SSL_read: Connection was reset, errno 10054`

解决方法：删掉项目根目录下的 `.deploy_git` 文件，重新执行命令 `hexo d` ,参考以下这篇博客：

https://www.cnblogs.com/tenderwx/p/5783432.html

