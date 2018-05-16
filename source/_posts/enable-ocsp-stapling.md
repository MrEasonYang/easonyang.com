---
title: 启用OCSP Stapling
tags:
  - HTTPS
  - Nginx
  - Apache
categories:
  - 教程
keywords:
  - ocsp stapling
  - ssl
  - apache
  - nginx
  - ssllabs
date: 2017-03-19 18:37:00
updated: 2017-03-19 18:37:00
---

## 前言

尽管相对于 HTTP ，HTTPS 在安全性上已经有了质的飞跃，然而简单的设置 HTTPS 仍无法避免类似于中间人攻击这样的恶意行为，而本文所要介绍的 OCSP Stapling 和 下篇文章将要介绍的 Certification Transparency 就是用来防范这些恶意行为的。

## 什么是 OCSP Stapling

OCSP (Online Certificate Status Protocol) 通常由 CA 提供，用于在线实时验证证书是否合法有效，这样客户端就可以根据证书中的 OCSP 信息，发送查询请求到 CA 的验证地址，来检查此证书是否有效。

然而这些默认查询 OCSP 的客户端在获得查询结果的响应前势必会一直阻塞后续的事件，在网络情况堪忧的情况下（尤其是大陆地区）会造成较长时间的页面空白。

而 OCSP Stapling ，顾名思义，是将查询 OCSP 接口的工作交给服务器来做，服务器除了可以直接查询 OCSP 信息，还可以仅进行少数次查询并将响应缓存起来。当有客户端向服务器发起 TLS 握手请求时，服务器将证书的 OCSP 信息随证书链一同发送给客户端，从而避免了客户端验证会产生的阻塞问题。由于 OCSP 响应是无法伪造的，因此这一过程也不会产生额外的安全问题。<!--more-->

## 如何开启

### Nginx

在配置文件的 server 块中添加如下内容：

```nginx
    ssl_stapling on;
    ssl_stapling_verify on;
    ssl_trusted_certificate /path/to/certs/chained.pem;
```

随后检测配置并重载：

```shell
nginx -t
nginx -s reload
```

### Apache

修改主机配置文件：

```xml
SSLStaplingCache shmcb:/tmp/stapling_cache(128000)
<VirtualHost *:443>
    SSLEngine on
    SSLProtocol all -SSLv3 -SSLv2

    SSLCertificateFile /path/to/your_domain.crt
    SSLCertificateKeyFile /path/to/your_private.key
    SSLCertificateChainFile /path/to/CA.crt

    SSLUseStapling on
</VirtualHost>
```

然后使用 `systemctl reload apache` 或 `service apache reload` 重载 Apache ：

## 验证 OCSP Stapling 是否开启

### 使用 SSLLAB 自动验证

访问 `https://www.ssllabs.com/ssltest/` ，输入欲测试的域名，稍等片刻后，如果发现结果中出现如下内容则证明已成功开启 OCSP Stapling ：

![ssllabs](/enable-ocsp-stapling/ssllabs.png)

### 手动验证

```shell
echo QUIT | openssl s_client -connect www.example.com:443 -status 2> /dev/null | grep -A 17 'OCSP response:'
```

若结果为以下内容，则开启成功

```
OCSP response:
======================================
OCSP Response Data:
    OCSP Response Status: successful (0x0)
    Response Type: Basic OCSP Response
    Version: 1 (0x0)
    Responder Id: C = US, O = Let's Encrypt, CN = Let's Encrypt Authority X3
    Produced At: Mar 18 04:34:00 2017 GMT
    Responses:
    Certificate ID:
      Hash Algorithm: sha1
      Issuer Name Hash: 7EE66AE7729AB3FCF8A220646C16A12D6071085D
      Issuer Key Hash: A84A6A63047DDDBAE6D139B7A64565EFF3A8ECA1
      Serial Number: 03EFDB2DC2103617A9CB5D33E1BE28CA880D
    Cert Status: good
    This Update: Mar 18 04:00:00 2017 GMT
    Next Update: Mar 25 04:00:00 2017 GMT
```

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。