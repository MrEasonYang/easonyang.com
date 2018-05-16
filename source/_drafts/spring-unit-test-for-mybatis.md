---
title: spring-unit-test-for-mybatis
date: 2016-08-13 23:37:47
updated: 2016-08-13 23:37:47
tags:
categories:
keywords:
---

最近迫不得已各种用 Mybatis ，遇到了一些之前忽视的问题。今天的问题是在没有接入互联网的情况下，Spring 的 Unit test 在对 Mybatis 相关代码进行单元测试的时候出现诡异的报错：

先是无法载入 XML 配置文件：

```
java.lang.IllegalStateException: Failed to load ApplicationContext
```

原因是：

```
Caused by: java.net.UnknownHostException: mybatis.org
	at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:184)
	at java.net.PlainSocketImpl.connect(PlainSocketImpl.java:172)
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
	at java.net.Socket.connect(Socket.java:589)
	at java.net.Socket.connect(Socket.java:538)
	...
```

我们知道 Mybatis.org 是 Mybatis 的官网，结合上面的无法载入配置文件，合理猜测

