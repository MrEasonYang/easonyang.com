---
title: 从SHA1值的获取初探Android中的Keystore
date: 2017-04-30 22:58:11
updated: 2017-04-30 22:58:11
tags:
categories:
keywords:
---

## 前言

作为一个业余 Android 爱好者，最近做的一款练手小应用需要用到高德地图的 API ，新建 API key 时，高德要求输入「发布版安全码SHA」，同时还有一个「调试版安全码SHA1」的可选输入项。

我们知道 SHA1 是一种数字签名算法，我们经常会在「数字证书」相关内容中听闻到他和他的兄弟们，根据高德的文档，也确实是获取 Android 应用数字证书的 SHA1 签名码。

## 获取调试版安全码SHA1

根据文档的指示，需要在 Android Studio 的 Terminal 中输入以下命令来查看签名：

`keytool -v -list -keystore keystore文件路径`

经多方查证，调试版的证书存储于 Windows 下的 ``或 Linux 下的  `~/.andoird/debug.keystore` 或 ``

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。