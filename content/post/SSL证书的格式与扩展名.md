---
title: SSL证书的格式与扩展名
url: 2017/03/20/format-and-suffix-of-ssl-certification
date: 2017-03-20 00:10:59
updated: 2017-03-20 00:10:59
tags: [HTTPS]
categories: [programming]
keywords: [https, ssl, pem, der, csr, cer, key, openssl]
---

## 编码格式

### PEM

- 内容：以 `-----BEGIN xxx-----` 作为开头，以 `-----END xxx-----` 作为结尾，主体部分使用 base64 对 ACII 进行编码。可以储存公钥证书（服务器证书及中检证书，「xxx」为 「CERTIFICATE」）和私钥（「xxx」为「RSA等加密算法名称 PRIVATE KEY」），也可以储存两者的联合，但通常情况下，Apache 等软件都会要求将公钥和私钥分开存储到不同的文件。
- 场景：*NIX 操作系统下普遍倾向于使用这一编码格式。
- 扩展名：`.pem` 、`.key` 、`.csr` 、`.cer` 、`.crt`
- 生成：`openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365` 扩展名参照上文
- 查看：`openssl x509 -in certificate.pem -text -noout` 扩展名参照上文

### DER

- 内容：由0、1组成的二进制数据，可以储存任何类型的公钥证书和私钥，不可读。
- 使用场景：Windows 系统 及 Java 平台倾向于使用这一编码格式。
- 扩展名：`.der` 、`.key` 、`.csr` 、`.cer` 、`.crt`
- 生成：`openssl req -x509 -newkey rsa:4096 -keyout key.der -outform der -out cert.der -days 365` 扩展名参照上文
- 查看：`openssl x509 -in certificate.der -inform der -text -noout` 扩展名参照上文

## 扩展名总结<!--more-->

- `.crt` 、`.csr` 、`.key` 多见于 PEM 编码的内容
- `.cer`多见于 DER 编码的内容

## PEM与DER间的转换

PEM 转换为 DER ：

```shell
openssl x509 -outform der -in certificate.pem -outform der -out certificate.der
```

DER 转换为 PEM：

```shell
openssl x509 -inform der -in certificate.der -outform pem -out certificate.pem
```