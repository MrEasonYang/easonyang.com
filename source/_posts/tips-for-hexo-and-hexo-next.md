---
title: 关于Hexo与其主题NexT的若干小问题与解决
tags:
  - Hexo
categories:
  - 教程
keywords:
  - Hexo
  - NexT
  - 教程
  - sitemap
  - local search
  - git
  - 404
date: 2016-08-03 16:46:52
updated: 2016-08-03 16:46:52
---


# Hexo

## 修改默认文章及草稿模板

默认情况下，我们使用 `hexo new draft xxx` 或 `hexo new post xxx` 创建的 md 文件中只有 title 、date 两个信息，常用的 tags 和 categories 等是没有的，只能在每个文件中手动添加，很是麻烦。要解决这个问题，只需要打开 Hexo 目录下的 `\scaffolds\post.md` 和  `\scaffolds\post.md` ，将内容修改为：

```
title: {{ title }}
date: {{ date }}
updated: {{ date }}
tags:
categories:
keywords:
```

这里除了 tags 和 categories 还添加了 updated （更新时间）和 keywords （关键字，用于自定义 SEO 关键字，留空的话则会使用 Hexo 默认设置）。<!--more-->

## 生成 Sitemap

sitemap 对 SEO 是至关重要的，在 Hexo 中生成 sitemap，首先要安装插件：

```
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```

后面的 baidu-sitemap 是对百度的优化，不是必选，厌恶百度的可以忽略。

随后在站点配置文件中添加如下内容即可：

```yaml
sitemap:
path: sitemap.xml
```

## 添加标签及分类页面

在很多主题中直接访问标签页面 `\tags` 和分类页面 `\categories` 会找不到页面，这是由于这两个页面是需要手动添加的。分别手动执行这两条命令即可：

```
hexo new page "tags"
hexo new page "categories"
```

## 向文章中添加本地图片

首先你需要在站点配置文件中开启文章的资源目录：`post_asset_folder: true` ，随后你生成的 draft 或 post 都会附带一个与文章同名的目录，图片放在这个目录中即可。如果你在 md 文件中直接使用该目录中图片的相对路径，虽然在编辑器中预览正常，但是最后生成的网页是看不到图片的。所以需要特殊处理。

首先是官方的方式，使用以下特有标签插入图片

```
{% asset_img slug [title] %}
```

但是这样做就不能在编辑器中预览图片了，而且也不太方便于以后可能发生的数据迁移。

个人推荐使用安装插件的方法，首先进行安装：`npm install https://github.com/CodeFalling/hexo-asset-image --save` 。然后就可以直接在 md 文件中使用 markdown 语法 `![]()` 引用图片的相对路径。这种方法在编辑器预览和生成的网页中都可以正常显示。

此外你也可以直接将图片放置于 `source\images` 下而无须上述的操作，代价是图片管理会变得极为复杂。

## 将 Hexo 部署到 Git 服务器

在站点配置文件中直接添加 deploy 选项即可，以添加 Github 为例：

```yaml
deploy:
- type: git
  repository: https://github.com/MrEasonYang/mreasonyang.github.io.git
  branch: master
```

不过这个功能隐藏的问题在于 Git 账户认证问题，部署时输入密码是难以接受的，所以应该在本机配置好公钥与私钥，参见 [为Git设置SSH公钥及私钥](https://eason-yang.com/2016/07/31/set-ssh-identity-file-for-git) 。

# NexT 主题

## 添加 404 页面

这个很好解决，将页面添加到主题的 source 目录下即可。

## 设置头像

默认头像是 `themes\next\source\images` 下的 avatar.gif，可以直接替换，但推荐将头像保存于 `themes\next\source\images` 或 `themes\next\source\uploads` ，随后在主题配置文件中添加：

```yaml
avatar: /uploads/avatar.gif
```

注意控制头像大小，避免影响加载速度。

## 设置代码高亮

在主题配置文件中修改 `highlight_theme` ，默认是 normal，本站使用 night eighties ，另外要注意 night bright 这个配色在代码被全选时与背景色差太小。

## 修改默认主题 keyword

在主题配置文件中可以看到，默认的 keywords 是 Hexo, NexT ，在生成的首页中也是这样显示的，建议根据需要添加自己的关键字来优化 SEO。

## 添加社交图标

在主题配置文件中修改 social 项，例如：

```yaml
social:
  GitHub: https://github.com/MrEasonYang
```

随后确保下面的 social_icons 设置了对应的值，键值为 FontAwesome 的图标名称，注意大小写，例如：

```yaml
social_icons:
  enable: true
  GitHub: github
```

## 添加 Local Search

安装插件 `npm install hexo-generator-search --save` ，随后在站点配置文件中添加：

```yaml
search:
  path: search.xml
  field: post
```

这个插件在其他主题也是可用的，只是需要手动添加一些前端样式，参见作者博客：[jQuery-based Local Search Engine for Hexo](http://hahack.com/codes/local-search-engine-for-hexo/) 。

## 为链接添加 nofollow

为了留住爬虫，SEO 的时候我们都会为链接加上 `rel="external nofollow"` 属性，然而 Hexo 和 Markdown 均不支持此功能，好在有前辈为我们做好了相关插件，详情参见：[Hexo优化之为外部链接添加nofollow](https://liuzhichao.com/2016/hexo-auto-nofollow.html) ，Github：https://github.com/liuzc/hexo-autonofollow

## 添加自定义菜单项

首先要在主题设置中的 menu 下新增一个值，如：`links: /url-of-links` ，此时前台可以看到新菜单项已经被成功添加，但是内容却是 menu.links ，menu 前缀的出现是缘于 hexo NexT 无法在语言文件中找到 links 对应的值，所以我们只需要在主题的 language 目录下找到站点所使用的语言文件，并在 menu 下添加 `links: 友情链接` 即可去掉 menu 前缀。

随后我们需要设置新菜单项的图标，在 http://www.fontawesome.com.cn/faicons/ 找到需要的图标后，添加到主题配置文件的 menu_icons 下，键名与 menu 中的对应，例如：`links: globe` ，这样一来，我们就成功添加了一个自定义菜单项。

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。