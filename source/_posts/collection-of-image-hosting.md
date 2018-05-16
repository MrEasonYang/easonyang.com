---
title: 常用图床及自建图床方法汇总
date: 2016-10-09 11:49:22
updated: 2017-12-09 11:49:22
tags: [图床]
categories: [资源与工具]
keywords: [图床, 公共图床, 自建图床, 微博图床, imgur, 图床推荐, image hosting, 常用图床]
---

## 公共图床

### 微博图床

国内图床的中流砥柱，速度非常快，服务也较为稳定，目前还支持了 HTTPS ，同时有大量的第三方工具方便使用，缺点是批量上传比较麻烦。

- [官方上传地址](http://photo.weibo.com/photos/upload)
- [新浪微博图床 Chrome 插件](https://chrome.google.com/webstore/detail/%E6%96%B0%E6%B5%AA%E5%BE%AE%E5%8D%9A%E5%9B%BE%E5%BA%8A/fdfdnfpdplfbbnemmmoklbfjbhecpnhf/related)
- [围脖图床 Chrome 插件（网页版不可用，Firefox 版在附加组件中搜索安装即可）](https://chrome.google.com/webstore/detail/%E5%9B%B4%E8%84%96%E6%98%AF%E4%B8%AA%E5%A5%BD%E5%9B%BE%E5%BA%8A/pngmcllbdfgmhdgnnpfaciaolgbjplhe/related?hl=zh-CN)

### imgur

09年就成立了的国外老牌图床，支持https，理论上不限制大小，还有邮件发图、视频转 GIF 、URL 上传等功能，很强大，可惜间歇性被墙，就算没被墙国内访问也很慢，面向国外的站点使用倒很合适。

地址：[https://imgur.com](https://imgur.com)

### sm.ms

支持https，有 API ，图片要求大小在 5MB 以下，一次上传不得超过10张，维护者是 [通天塔](https://ttt.tt) 的站长。主站 https://sm.ms 目前用的是阿里云新加坡的机器，而上传的图片则会被存储到使用阿里云香港的 https://ooo.0o0.ooo 中，因此速度还是很不错的。<!--more-->

地址：[https://sm.ms](https://sm.ms)

API：[https://sm.ms/doc/](https://sm.ms/doc/)

### otar.im

这个网站本身并不是一个图床，但却做到了一个更比三个强，整合上文提到的微博图床、imgur、sm.ms 到一个页面中，非常方便实用。然而最近使用时提示未知错误上传失败，不知是否停止维护了。

地址：http://upload.otar.im/

### V2EX 图片库

程序员社区 v2ex 推出的付费图床服务，由于博主没有需求所以未付费使用过，效果不明。

地址：https://www.v2ex.com/i/about

### LightPic

使用其自己开发的系统，站点规则中写明了不保证图片可以永久存储，似乎使用了 CloudFlare 非中国版的 CDN ，因此支持 https ，但是 CF 在国内部分地区的访问效果很堪忧。

地址：https://lightpic.info

### picwindow

国外图床，号称长久而稳定的储存，图片要求大小在 10MB 以下，国内访问速度一般但可以接受，缺点是上传时有些慢，且不支持 HTTPS ，不过他的远程上传（其实也就是通过 URL 直接上床图片）的功能还是很好用的。

地址：http://www.picwindow.com/

### 贴图库

早年的神级图床，可惜后来因为某种原因关停，恢复后暂时使用了 kekaoyun 这个域名，现在已经切换回了原域名，但是也从关停前的永久免费变为了6个月免费，也就是免费存储的图片只保存六个月，并提供了付费的商业选项。速度没得说，但是不适合博客这样的站点使用。

地址：http://www.tietuku.com/

费用详情：http://www.tietuku.com/open

### 萝莉图床

也是早年一个很好用的图床，但是现在需要邀请注册才能使用。

地址：http://loli.io/

## 自建图床

### 七牛、又拍云等云存储

这些服务商其实是做 CDN 和云存储的，但是由于有一定的免费存储及流量额度，所以也有很多人拿来做自己博客的图床，速度可以和微博图床媲美，各种以其为基础开发的第三方工具用起来也很顺手，缺点是大部分都不支持 https ，同时注册时要求上传身份证信息挺麻烦的。下面推荐几个具有代表性的服务商及第三方工具：

服务商：

- 七牛：http://www.qiniu.com/
- 又拍云：https://www.upyun.com/index.html

工具：

- iPic (Mac only)：http://i.toolinbox.net/iPic/iPicUploader.html
- yotuku：http://yotuku.cn/
- PHP版七牛图床：https://github.com/4679/qiniu-picbed
- Get Link：https://github.com/int64ago/getlink

### 树洞外链

> 树洞外链是一款免费开源的PHP外链网盘系统，界面简洁友好，支持七牛、本地、远程、阿里云OSS、又拍云五种储存方式，支持多用户系统，多上传方案策略。

官方给出了这样的说明，实际使用下来也确实不错，有文档、安装方便也比较稳定，但存在着一些小 Bug ，小站点用来自建图床还是很合适的。

官网：https://yun.aoaoao.me/

Github：https://github.com/HFO4/shudong-share

### Lychee

一款开源的图片管理系统，实际上也可以用作图床，一直在更新，自身功能很强大，还支持二次开发插件及扩展来丰富功能，此外这套系统也支持 Docker 。

GIthub：https://github.com/electerious/Lychee



本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。