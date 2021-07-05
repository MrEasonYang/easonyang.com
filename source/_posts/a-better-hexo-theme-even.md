---
title: 记hexo-theme-even主题优化
date: 2021-07-06 01:04:28
updated: 2021-07-06 01:04:28
tags: [hexo]
categories: [教程]
keywords: [hexo, theme, even, 主题, 优化]
---
最近经常看到 Hugo 用户使用 [hugo-theme-even](https://themes.gohugo.io/themes/hugo-theme-even/) 主题搭建的博客，简洁的外观和明亮的配色很快吸引了我。随手一搜发现 Hexo 下也有同款主题，马上就给本博客换上了。不过对我个人而言，这款简洁的主题的一些小细节不能使我满意，于是就动手做了些小优化。
<!--more-->
# 更丰富的图标库
此款主题使用的是作者自定义的 iconfont 图标库，如其 FAQ 所述，新的图标需要作者的额外支持，目前支持如下图标：

- email
- stack-overflow
- twitter
- facebook
- linkedin
- google
- github
- weibo
- zhihu
- douban
- pocket
- tumblr
- instagram

这个列表已经很全了，但是好事不嫌多，我们其实也可以如其他主题一样使用图标超丰富且免费的 [Font Awesome](https://fontawesome.com/) 来进行扩展。

从 Font Awesome 官网[下载](https://fontawesome.com/v5.15/how-to-use/on-the-web/setup/hosting-font-awesome-yourself)完整的文件包，解压后将该目录移动至 `even/source/lib` 下即可，当然我们也可以选择使用各类 CDN 来节省流量，不过由于主题自带的 iconfont 在本地调试时的加载速度会快过 CDN 进而造成视觉上不一致的观感，所以身为「强迫症患者」，我还是推荐直接本地安装。

随后在 `even/layout/_partial/head.swig` 中已有的 CSS 引用下新增如下内容：

```
...
{#- Font Awesome -#}
<link rel="stylesheet" type="text/css" href="{{ url_for('lib/fontawesome/css/all.min.css') }}">
...
```

这样一来我们就可以直接在需要的地方使用 Font Awesome 的海量图标了。例如页面底部的社交图标可以通过主题配置增加如下内容来新增微信公众号和 Telegram 的社交链接：

```
		...
    {%- if theme.wxOfficialAccount -%}
      <a href="{{ url_for(theme.wxOfficialAccount.url) }}" class="iconfont" title="微信公众号"><i class="fab fa-weixin"></i></a>
    {%- endif -%}

    {%- if theme.telegram -%}
      <a href="{{ url_for(theme.telegram) }}" class="iconfont" title="Telegram"><i class="fab fa-telegram"></i></a>
    {%- endif -%}
		...
```

# 支持站点访问统计
这项功能我直接使用[不蒜子](https://busuanzi.ibruce.info/)来实现。在主题配置中新增 `busuanzi` 选项并配置为 `true` ，在负责计数的 `even/layout/_script/counter.swig` 中引入不蒜子的依赖：

```
...
{%- if theme.busuanzi -%}
  <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
{%- endif -%}
...
```

最后在 `even/layout/_partial/footer.swig` 中加入如下内容即可：

```
	...
  {%- if theme.busuanzi -%}
    <span style="display: block;">
      <span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
    </span>
  {%- endif -%}
  ...
```

# 去除百度链接提交
不知为何，此款主题默认开启了百度链接提交的功能且未提供配置项，页面加载时会尝试加载 `https://zz.bdstatic.com/linksubmit/push.js` ，如果想关闭这一配置只需要在 `even/layout/_script/push.swig` 中的最外层加入一层 if 判断即可，修改后的代码如下：

```
{%- if theme.baidu_push -%}
    <script id="baidu_push">
    (function(){
        var bp = document.createElement('script');
        var curProtocol = window.location.protocol.split(':')[0];
        if (curProtocol === 'https') {
            bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';
        }
        else {
            bp.src = 'http://push.zhanzhang.baidu.com/push.js';
        }
        var s = document.getElementsByTagName("script")[0];
        s.parentNode.insertBefore(bp, s);
    })();
    </script>
{%- endif -%}
```

# 给文末加个小广告
虽然影响阅读体验，而且我个人也不喜欢微信公众号的封闭，但最近还是打算给文章的结尾都加上个微信公众号的二维码，大部分主题都没有直接支持，需要额外开发。

对于本款主题，首先可以新增模版 `even/layout/_partial/_post/custom-footer.swig` ，这样我们就能在这里使用 HTML 随意定制文末的样式。

随后在 `even/layout/_macro/post.swig` 中的版权模块下引入 `custom-footer.swig` 即可：

```
		...
    {%- if not is_home() -%}
      {#- Post Copyright -#}
      {%- include "../_partial/_post/copyright.swig" -%}

      {#- Custom footer -#}
      {%- include "../_partial/_post/custom-footer.swig" -%}

      {#- Reward -#}
      {%- include "../_partial/_post/reward.swig" -%}
    {%- endif -%}
 		...
```

# 让标题更明显些
我有个不太好的写作习惯，那就是文章的大章节标题会使用一级标题（对应到 Markdown 就是 # ），这就导致在本款主题的默认样式下，文章标题与章节标题傻傻分不清楚。

对于这个问题，作者在 `even/source/css/_custom/_custom.scss` 提供了 CSS 重写的功能。但由于懒得去找对应的 class 及其 CSS 定义，我直接简单粗暴地选择使用 `!important` 来强制覆盖：

```
.emphasized-title {
    font-weight: bolder !important;
}
```

接着在 `even/layout/_macro/post.swig` 中为 `h1` 标签新增这个 class 即可：
```
			...
      <h1 class="post-title emphasized-title">
        {%- if is_home() -%}
          <a class="post-link" href="{{ url_for(post.path) }}">{{ post.title }}</a>
        {%- else -%}
          {{ post.title }}
        {%- endif -%}
      </h1>
      ...
```

# 总结
总体来说这款主题选用了 hexo 主题实现中常见的 Swig 和 Sass ，对于主题场景来说比之前使用的 [hexo-theme-icarus](https://github.com/ppoffice/hexo-theme-icarus) 的 React 实现要好上手得多。同时目前该项目还在不断接受 PR ，并一直有小幅迭代，未来还是值得期待的。后面我也会把本文涉及的改动通过 PR 或 fork 的形式整理出来供需要的朋友直接使用。