---
title: 简易增强版hexo-theme-even
date: 2021-08-01 20:07:59
updated: 2021-08-01 20:07:59
alias: 2021/08/01/enhanced-hexo-theme-even
dir: /
tags: [hexo]
categories: [教程]
keywords: [hexo, hexo-theme-even, 增强版, hexo主题]
---
Even 主题的简洁深得我心，它也是本博客当前的主题。不过有许多常用功能在原主题中并未直接支持，因此就自己 fork 后动手做了些小的体验优化和功能实现，供有类似功能需要的朋友直接使用。优化的部分思路可以参考[前文](https://easonyang.com/2021/07/06/a-better-hexo-theme-even/)。

当然我只做了很小的一部分优化，主题作者 [Yuexun Jiang](https://github.com/ahonn) 的主题代码创建和维护才是最核心的贡献。后续我也会通过 PR 的形式将本文内容中的通用部分提交到原主题中。

下文为增强版 even 主题的中文使用说明。<!--more-->

## 地址
- 原主题：[ahonn/hexo-theme-even](https://github.com/ahonn/hexo-theme-even)
- 简易增强版主题：[MrEasonYang/hexo-theme-even](https://github.com/MrEasonYang/hexo-theme-even)

## 增强版功能
- 支持使用 Font Awesome 作为图标库
- 支持使用不蒜子（busuanzi）进行站点访问量统计
- 支持文末的站内热点文章推荐
- 支持整站字数的统计和展示
- 将百度推送置为可选项
- 支持自定义页脚，可添加微信公众号信息等内容
- 加重了首页文章列表中的标题字体以便能更加清晰地区分标题和文章内容
- 将版权信息放置于文章内容中以对抗不遵守协议的爬虫
- 修正了原主题在文章列表中获取访问次数时会发起多次请求的问题
- 支持配置 Twitter Cards
- 支持 Open Graph 协议
- 支持搜狗和神马搜索的站点验证
- 可在页脚添加站点地图链接
- 在文章列表中使用 h2 标签替换 h1 标签以符合 Bing 等平台的 SEO 要求

## 原主题功能
原主题的所有功能均支持，可查看原主题文档进行配置：[hexo-theme-even docs](https://github.com/ahonn/hexo-theme-even/wiki)

## 增强功能的使用
### 使用 Font Awesome 图标
原主题的图标为作者自定义的 iconfont 图标库，新图标的增加需要作者进行额外支持。

增强版引入了 Font Awesome 依赖，从而实现可以使用 [Font Awesome 网站](https://fontawesome.com/) 上的任意图标。

例如，增强版原生支持了 Telegram 页脚社交图标的展示，在主题配置中添加以下内容即可：
```yaml
telegram: <Telegram chat url>
```

### 使用不蒜子进行站点统计和展示
添加以下内容到主题配置文件即可开启：
```yaml
busuanzi: true
```

### 展示站内热点文章推荐
1. 安装依赖：`npm install hexo-related-popular-posts -S`
2. 阅读该插件的文档以了解如何进行参数配置 [hexo-related-popular-posts docs](https://github.com/tea3/hexo-related-popular-posts)
3. 添加以下内容到主题配置文件即可开启推荐功能:

```yaml
popular_posts:
  enable: true
  maxCount: 5
  PPMixingRate: 0.5
```

### 页脚添加微信公众号信息
添加以下内容到主题配置文件即可开启：
```yaml
wxOfficialAccount:
  enable: true
  url: <The QRCode image url>
```

### 设置 Twitter Cards
1. 设置 Twitter Cards 后，在 Twitter 中发送的链接可展示预览信息，详见[Twitter cards docs](https://developer.twitter.com/en/docs/twitter-for-websites/cards/overview/abouts-cards)
2. 添加以下内容到主题配置文件即可开启:

```yaml
twitter_card:
  style: <See Twitter card docs>
  creator: <Twitter username>
```

### 设置 Open Graph
1. 阅读文档：[Open Graph docs](https://ogp.me/)
2. 添加以下内容到主题配置文件即可开启:

```yaml
open_graph:
  type: <See https://ogp.me/#types>
```

### 整站字数统计与展示
1. 安装依赖： `npm install hexo-wordcount -S`
2. 添加以下内容到 hexo 配置中即可开启

```yaml
word_count: true
```

### 在页脚展示站点地图
添加以下内容到主题配置文件即可开启：
```yaml
footer_sitemap: true
```

### 开启搜狗和神马搜索的站点验证
添加以下内容到主题配置文件即可开启：
```yaml
# Sogou verification
sogou_verification:

# Shenma verification
shenma_verification: 
```

### 停止百度推送
增强版默认关闭了百度推送功能，添加以下内容到主题配置文件中即可重新开启：
```yaml
baidu_push: true
```

### 修复 Leancloud 计数器
原始实现使用了旧版的 Leancloud CDN ，其中的 API 目前已经 404 了。为保证计数器可用，主题升级和更换了 CDN 地址，同时支持了自定义域名以解决目前 Leancloud 要求中国区应用需要使用特定域名的问题。
```yaml
# LeanCloud
leancloud:
  app_id: <Your Leancloud appId>
  app_key: <Your Leancloud appKey>
  server_url: <Your Leancloud domain>
```