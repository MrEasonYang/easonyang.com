---
title: 支持HTTPS的公共CDN汇总
date: 2016-11-07 10:58:52
updated: 2016-11-07 10:58:52
tags:
categories:
keywords:
---

# 国内

## [Staticfile CDN](https://www.staticfile.org/)

- 背景：七牛
- 速度：极快
- 内容：常用 JS、CSS 等静态文件的最新版本
- 来源：同步国外 [CDNJS 源站](https://github.com/cdnjs/cdnjs)，同时由国内开源贡献值提交其它有价值的库
- 安全性：COMODO签发，测试等级： [A-](https://www.ssllabs.com/ssltest/analyze.html?d=cdn.staticfile.org&hideResults=on)
- HTTP2：不支持
- 注：可能借助到了七牛的全球 CDN ？在世界范围内的访问速度都很可观

## [BootCDN]()

- 背景：[Bootstrap中文网](http://www.bootcss.com/) 、Upyun
- 速度：极快
- 内容：常用 JS、CSS 等静态文件的最新版本
- 来源：所收录的开源项目主要同步于 [cdnjs](https://github.com/cdnjs/cdnjs) 仓库，未找到直接提交库的方式
- 安全性：Let's Encrypt 免费证书，测试等级： [A](https://www.ssllabs.com/ssltest/analyze.html?d=cdn.bootcss.com&s=165.254.60.146&hideResults=on)
- HTTP2：不支持

## [ 360 前端静态资源库 Baomitu](https://cdn.baomitu.com/)

- 背景：360
- 速度：快
- 内容：常用 JS、CSS 等静态文件的最新版本，Google Fonts
- 来源：同步于 [cdnjs](https://github.com/cdnjs/cdnjs) ，提交新库需要在[ cdnjs ](https://github.com/cdnjs/cdnjs)提 Pull Request
- 安全性：沃通签发，测试等级：[F](https://www.ssllabs.com/ssltest/analyze.html?d=lib.baomitu.com&hideResults=on)
- HTTP2：支持
- 特色：相比于其他公共 CDN ，还提供了 Google 字体库
- 注：实际上这个产品是 libs.useso.com 的延续与升级

## [又拍云 JS 库](http://jscdn.upai.com/)

- 背景：又拍云
- 速度：极快
- 内容：只提供 jQuery 、 MOOTOOLS 、 MODERNIZR 、 DOJO 和 EMBER 的旧版本
- 来源：未知
- 安全性：GeoTrust SSL CA 签发，测试等级：[A](https://www.ssllabs.com/ssltest/analyze.html?d=upcdn.b0.upaiyun.com&s=165.254.60.146&hideResults=on)
- HTTP2：不支持





本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。