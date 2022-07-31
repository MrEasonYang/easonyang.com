---
title: 为Hexo NexT主题添加字数统计功能
url: 2016/11/05/add-word-count-to-hexo-next
date: 2016-11-05 23:25:30
updated: 2016-11-05 23:25:30
tags: [Hexo]
categories: [course]
keywords: [Hexo, NexT, hexo-wordcount, 字数统计, wordcount, 教程]
---

## 安装 hexo-wordcount 插件

首先在博客目录下使用 npm 安装插件：

```shell
npm install hexo-wordcount --save
```

## 修改配置文件

为了方便地开启和关闭字数统计功能，我们需要在配置文件（站点配置文件或主题配置文件均可）中添加一个键值对：

```yaml
# 开启字数统计
word_count: true

# 关闭字数统计
# word_count: false
```

## 修改主题 swig 布局

### 修改 post.swig

为了能在文章信息处显示字数，我们需要修改 `themes/next/layout/_macro/post.swig`，在 class 为 `post-mata` 的 div 中的添加如下内容：<!--more-->

```html
          {% if theme.word_count %}
            <span class="post-letters-count">
              &nbsp; | &nbsp;
              <span>{{ wordcount(post.content) }} 字</span>
              &nbsp; | &nbsp;
              <span>{{ min2read(post.content) }} min</span>
            </span>
          {% endif %}
```

### 修改 footer.swig

想要在页面底部网站信息处显示字数统计，就要修改 `themes/next/layout/_macro/post.swig` ，在文件的最后添加如下内容：

```html
<div class="theme-info">
  <div class="powered-by"></div>
  <span class="post-count">共{{ totalcount(site) }}字</span>
</div>
```

## 修改 CSS 以实现移动端隐藏字数统计

对于移动端尤其是手机来说，加上字数统计和阅读时间预估后的文章信息显得过于冗长，在稍微小一些的屏幕上甚至会分两行显示。

而如果我们仔细观察会发现，使用 Hexo NexT 主题的站点如果开启了评论，那么文章信息处的评论数只会在大尺寸屏幕上显示，在小尺寸屏幕上会直接隐藏掉，这显然是利用了 CSS 的 @media 来适配不同尺寸屏幕，因此我们只需要取巧地在原主题地 CSS 的 @media 中完成隐藏 `.post-letters-count` 这个类就可以达成我们的目的。

修改位于 `themes/next/source/css/_common/components/post` 目录下的 `post-collapse.styl` 文件，在文件前几行找到 @media 并修改为：

```css
@media (max-width: 767px) {
  .post-letters-count { display: none;}

  .posts-collapse {
    margin: 0 20px;

    .post-title, .post-meta {
      display: block;
      width: auto;
      text-align: left;
    }
  }
}
```

## 后记

至此我们就成功地为 Hexo NexT 主题添加了字数统计功能。对于其他主题尤其是并非使用 swig 模板引擎的主题，可以参照插件的 sample 进行修改：https://www.npmjs.com/package/hexo-wordcount 。