---
title: "OTP与Authenticator"
date: 2023-02-19T17:23:46+08:00
lastmod: 2023-02-19T17:23:46+08:00
draft: true
slug: "otp-and-authenticator"
keywords: ["OTP", "HOTP", "TOTP", "2FA", "双因素验证", "Google Authenticator", "Microsoft Authenticator", "Authy", "YubiKey"]
description: ""
tags: []
categories: []
images: []
---
马斯克最近又搞了个蜜汁操作，将短信验证从 Twitter 的 2FA 安全验证方式中剔除，有趣的这项变化仅针对苦逼的广大 Twitter 免费用户，Twitter Blue 用户还能接着用。我不想评价这个将基础安全功能划为安全专项的操作有多奇葩，但打算借这个机会梳理下 目前 Twitter 2FA 中仅存的 Authentication app 验证能力和其背后的 OTP 机制。

## 什么是OTP

OTP 是 One-Time Password 的缩写，是一种基于动态口令技术的身份认证机制，使用一个动态口令生成器来生成一次性密码，用户在进行身份验证时需要输入当前的一次性密码，系统根据事先共享的密钥信息和算法来验证用户输入的密码是否正确。在互联网 toC 场景中，由于 Google Authenticator 的做功，大量国外企业都使用该方式作为双因素验证中的第二个因素，以加固账号安全；而在金融等场景中，我们熟悉的网银等各类硬件密码生成器也是该机制的衍生形式。OTP 可以分为 HOTP(HMAC-based One-Time Password) 和 TOTP(Time-based One-Time Password) 两类，后者更像是前者的简化版本。

### HOTP的原理

抽象来看，HOTP 可以简单概括成下面这个公式：

$$
HOTP(K,C) = Truncate(HMAC(K,C))
$$

其中 K 即密钥 C 为计数器，翻译过来就是密钥和计数器进行哈希，随后对哈希结果截取一段作为一次性密码。而哈希的方式使用 HMAC 来进行，RFC 原文的示例是 HMACSHA1 ，不过在当前 SHA1 已理论上不再安全，应该至少选择 HMACSHA256 。可见对于同一个用户来说，Truncate 、HMAC 和 K 是固定的，变数只有 C ，而 HOTP 的安全性也是由这个计数器在每次使用后递增来保证的。



### TOTP的原理



## Authenticator验证器的选择