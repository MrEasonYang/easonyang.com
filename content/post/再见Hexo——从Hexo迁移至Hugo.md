---
title: "再见Hexo——从Hexo迁移至Hugo"
date: 2022-08-10T01:36:15+08:00
lastmod: 2022-08-10T01:36:15+08:00
draft: false
slug: "hexo-to-hugo"
keywords: ["Hugo", "Hexo", "静态博客", "博客系统迁移", "even主题"]
description: ""
tags: ["hugo"]
categories: ["course"]
---
16 年的时候，博客使用的虚拟主机需要做迁移，当时所使用的 Typecho 是一个依赖于数据库的 PHP 博客系统，数据导出过程很艰辛。彼时 Gihub Pages 正大火，我也就跟风转投了静态博客系统 Hexo 。七年过去了，博客还在，但折腾 Hexo 的人是越来越少了。如今我也要和 Hexo 说声再见，拥抱 Hugo 的怀抱了。<!--more-->

## 为什么改用Hugo

### 依赖管理问题

在[静态博客的同步和备份方案](https://easonyang.com/2021/07/10/how-to-sync-and-backup-a-static-blog/)一文中，我分享了基于 Github 和 iCloud 的同步备份方案，期望在保证数据安全的同时，写作环境可以在我的两台电脑上无缝切换。

然而实际上，由于 Hexo 依赖于 Node.js ，庞大的 node_modules 并不能直接同步而是需要分别安装和更新，如果忘了运行 `npm install -S` ，那报错和渲染异常就是家常便饭的事。而 node-sass 这样的库还对 node 版本做了显式要求，导致如果哪台设备改了 nvm 中 node 的版本就会报错。但偏偏又有大量的主题和插件都依赖于 node-sass 。

而 Hugo 是基于 Golang 的二进制程序，安装和升级都很简单。由于内置功能足够多，插件（模块）不再是必需的了，如果有需要，其也都是通过 go mod 管理，轻量而简洁。虽然大部分主题仍然依赖于 Node.js ，但那只是创建和修改主题时的事情，不会影响到写作流程。

诚然，这并不能算作 Hexo 本身的问题，但由于底层的技术选型，导致 Hexo 必然和各类主题及插件的耦合较为严重。在三方组件实现和依赖复杂的情况下，整体的复杂度也就必然会成倍地上涨。

### 网页生成速度

Hexo 裸安装后的网页生成速度并不算不可接受，和 Hugo 比起来也就几秒到十几秒的差异，没有网上传得那么夸张。但多加了几个像 [hexo-all-minifier](https://github.com/chenzhutian/hexo-all-minifier) 这样生命周期靠后的插件后，生成速度确实会肉眼可见地下降。而 Hugo 的网页生成速度则非常稳定，总是保持在秒级别甚至毫秒级别，因此也可以真正意义上地实现本地实时预览。

### Hugo的特性

Hugo 对 org-mode 、pandoc 等提供了原生支持，轻度使用体验也不错（深度使用也会遇到坑）。虽然 Hexo 等也可以通过安装插件和转换器等方式来实现，但这又会回到上面的依赖复杂的问题之中。此外，Hugo 的 [shortcode](https://gohugo.io/content-management/shortcodes/) 功能也非常强大，如果不考虑 md 文件的通用性，那结合 shortcodes 可以轻松实现很多本需要依赖于插件（模块）才能做到的功能。

## 快速上手

首先安装 Hugo 并创建站点，以 macOS 系统为例：

```shell
brew install hugo # For macOS
hugo new site sample
```

这里需要注意的是，如果此前系统中已经安装过旧版本的 Go ，那有可能需要升级后才能使用 homebrew 完成安装。随后选一个喜欢的主题拉取到 `/themes` 目录，以 even 主题为例：

```shell
git clone https://github.com/olOwOlo/hugo-theme-even themes/even
```

主题通常会带有示例配置文件 `config.toml` ，将其复制到站点的根目录下覆盖默认配置文件，并完成相应配置后，运行 `hugo new post/test.md` 新建文章，随后运行 `hugo server -D` 即可查看站点。对于存量的文章，需要将其复制到主题 `/content/` 目录下，其中文章类的需要按主题的设计来放置于具体目录，如 even 主题使用 post 目录，则需要将文章复制到 `/content/posts` 目录下，重新执行上面的命令就能在站点里看到文章了。

如果运行命令时报错，则可能是存量文章的 front matter 格式有不符合 Hugo 要求的情况，此时需要按[官方文档](https://gohugo.io/content-management/front-matter/)进行修改适配。

## 写作习惯的变化

整体写作习惯其实和 Hexo 的体验差别不大。不过 Hugo 提供了比 Hexo 更丰富的 front matter 默认配置，同时还支持 org-mode 等玩法，所以写作方式上的可玩性会更高些。

## 主题自定义方式的变化

与 Hexo 主题的完全前端实现不同，Hugo 的主题使用了 Go 的模板语言，有点类似于 PHP 和 JSP ，并向主题暴露了一系列的全局变量和函数，所以其实主题和 Hugo 或 Golang 还是有一定的耦合的。

不过这也使得我们可以通过自定义模板覆盖主题默认模板的方式，来既实现自己的需求，又能最低限度地避免修改主题源代码导致的升级困难。但很多主题没有提供关键位置的钩子模板（也可以说是接口），导致我们经常需要拷贝一部分主题的源代码到自定义模板中，这又对主题的升级造成了一定的影响，可以说是有得必有失了。

## 向前兼容

迁移工作的一个核心要求就是尽量避免引入 breaking change 。首先要保证存量页面的链接不发生变化，以避免出现 404 的情况。其次要尽量对此前在 Hexo 中使用的各项功能进行支持。

### 永久链接格式兼容

在 Hexo 中，通常有以下三种 URL 永久链接路径格式：

1. **日期前缀+英文别称或文件名**：`/2021/07/06/a-better-hexo-theme-even/`
2. **固定前缀+英文别称或文件名**：`/posts/slidev-tutorial/`
3. **使用了 abbrlink 等插件生成 CRC/Hash 值作为路径**：`/posts/8ccq01298/`

而 Hugo 默认的永久链接格式为 `/{{ 文章目录 }}/{{ 文章文件名 }}` ，和上面第二种比较相似但又有所不同。那么我们该如何实现兼容呢？

首先我们要了解 Hugo 可以在根目录的 `config.toml` 中对永久链接进行自定义配置，例如：

```toml
[permalinks]
  '/' = "/posts/:slug"
```

因此我们只需要针对不同情况，对该配置进行自定义即可。

1. **日期前缀+英文别称或文件名**：仿照 Hexo 的日期格式，将值配置为 `/:year/:month/:day/:title/` 。 
2. **固定前缀+英文别称或文件名**：将值改为 `/posts/:title` 即可。 Hugo front matter 中的 `slug` 变量表示自定义别名，所以如果此前在 Hexo 使用了自定义的变量，只要仿照此前的配置将 title 改为 slug 即可，例如 `/posts/:title` 。
3. **使用 abbrlink 等插件生成的 CRC/Hash 值作为路径**：Hugo 似乎没有 abbrlink 这类插件，不过我们可以仿照[这篇文章](https://blog.lxdlam.com/post/9cc3283b/)，在默认内容模板 `archetypes/default.md` ，再将其中的 slug 配置为一段具备哈希或 CRC 功能的表达式即可。不过存量文章可能需要通过 front-matter 中的 `url` 变量进行完整路径的显式声明，不然如果表达式的处理结果和 Hexo 中的不同，那链接可就变了。

上面说明了 `permalinks ` 的值，那 key 该如何配置呢？与 Hexo 不同，Hugo 中永久链接的固定前缀（对应上文的情况 2 和情况 3）是根据目录位置生成的，该位置的选择又与主题有关。有的主题使用的是 `/content/post/` 目录，有的主题使用的又是 `/content/posts` 目录，同时这个路径在很多主题的实现中是写死的。因此如果此前你在 Hexo 中所使用的固定前缀和所选 Hugo 主题的不同（如 `/articles/`），那就会造成链接发生变化的问题。

所以我们需要在 `permalinks ` 配置的 key 上做些文章，把主题所用的路径做一层指定映射，保证最终的路径以我们期望的前缀输出。以主题使用 `/content/post/` 作为内容目录、原 Hexo 文章的永久链接格式为 `/posts/:slug` 为例，对 `permalinks `  进行以下配置即可：

```toml
[permalinks]
  post = "/posts/:slug"
```

另外，对于此前在 Hexo 中配置了 html 后缀等情况，可以开启 Hugo 的 Ugly URLs 来实现兼容，细节可以参考[官方文档](https://gohugo.io/content-management/urls/#ugly-urls)。

### 归档页面路径链接的兼容

前一节提到了主题对内容目录路径的选择可能是不同的，而这也会影响到归档页面的路径。在 Hugo 的大部分主题中，如果主题使用 `/content/post/` 作为内容目录，那归档页面路径则默认为 `/post/` 且不支持配置。而我们在 Hexo 中通常会使用 `/archives/` 作为归档页面的路径，如何才能保持不变呢？

虽然绝大部分主题（也可能是所有）都没有此项配置，但我们可以通过自定义一套模板和页面的方式来绕过主题的限制。以 even 主题的模板和 CSS 样式为例，在 `/layouts/_default/` 目录下新建 `archives.html` 模板，随后填充以下内容，用于按年分组遍历所有文章，并在原主题的框架下输出文章标题列表：

```html
{{- define "title" }}{{ T "archive" }} - {{ .Site.Title }}{{ end -}}

{{- define "content" }}

{{ $pageList := (where .Site.RegularPages "Type" "post") }}

<section id="archive" class="archive">
  {{- if .Site.Params.showArchiveCount }}
    <div class="archive-title">
      <span class="archive-post-counter">
        {{ T "archiveCounter" (len $pageList) }}
      </span>
    </div>
  {{- end -}}

  {{ range ($pageList.GroupByDate "2006") }}
    <div class="collection-title">
      <h2 class="archive-year">{{ .Key }}</h2>
    </div>

    <ul class="archive-list">
      {{ range (where .Pages "Type" "post") }}
        <div class="archive-post">
          <span class="archive-post-time">
            {{ .PublishDate.Format "01-02" }}
          </span>
          <span class="archive-post-title">
            <a href="{{ .RelPermalink }}" class="archive-post-link">
              {{ .Title }}
            </a>
          </span>
        </div>
      {{ end }}
    </ul>
  {{ end }}
</section>
{{ end }}
```

随后在 `/content/` 目录下新建 `archives.md` 页面，将 `type` 指定为刚刚定义的 `archives` ：

```markdown
---
title: "归档"
layout: "archives"
url: "/archives/"
comment: false
hidden: true
type: archives
---
```

这时我们就已经可以通过访问 `/archives/` 路径来进入到归档页面了，接下来只要在 `config.toml` 中再将导航栏中的对应按钮指定为预期链接即可。

```toml
[[menu.main]]
  name = "归档"
  weight = 20
  identifier = "archives"
  url = "/archives/"
```

上文这种实现的效果和很多主题的归档页面相比，主要区别在于单页面内罗列了所有文章，即缺少分页。由于大部分主题的分页逻辑和其内部的其他模板耦合较为严重，同时 Hugo 的分页相关变量被限制不能用于自定义模板之中，所以如果希望自定义的归档页能支持分类，则可能需要对 Hugo 的原生逻辑进行包装即额外实现一套分页能力才行。这里不做展开讲述。

### 友情链接和自我介绍

和 Hexo 一样，Hugo 也没有直接支持友情链接和自我介绍这类常用页面。在实现上我们要么在 `/content/` 目录下自定义页面也就是在页面内维护内容，要么如归档页面一般，通过自定义模板的方式来加载 `config.toml` 中的配置。两种实现都比较简单，我也更倾向于前者，毕竟这些是低频修改页面，是否可配置区别都不大。

### 标签和分类的中英文问题

在 Hexo 中，我们通常会在 `_config.yml` 中配置标签和分类的中英文映射，这样我们在 front matter 中可以使用任意语言标识标签和分类，但生成后两者的 URI 都是英文。然而在 Hugo 中却没有这类简易设置，也许我们可以通过修改主题和永久链接的方式来间接支持，但估计成本较高。所以如果对 URI 有强迫症的读者，还是建议把存量文章的标签和分类改为英文。而如果对此没有特殊需求，那使用中文也可。除了 URL 的分享可读性可能较差外，在 2022 年的今天其实已经不会影响搜索引擎的 SEO 效果了。

### 支持Git与VPS部署

不知为何 Hugo 官方没有直接支持使用 Git 搭配 Git Hooks 部署站点，对于我这种把博客部署在 VPS 的用户给出的建议方案是 rsync 。其实 rsync 方案是完全可行且成本不高的，不过本着尽量兼容的原则我还是决定在部署时执行以下 shell 脚本来通过 Git 推送生成的 `/public/` 目录至 VPS ，而 VPS 上的 Git 库和 Git Hooks 配置则无需改动：

```shell
#! /bin/bash

rm -rf ./public
hugo

rm -rf ./.deploy_git
mkdir .deploy_git
cp -r public/* ./.deploy_git
cd .deploy_git

git init --initial-branch=master
git add -A
git commit -m 'Deploy commit.' --quiet
git push -u foo@bar-server:/var/blog.git HEAD:master --force

cd ..
rm -rf ./.deploy_git
```

### 兼容Hexo的RSS形式

使用 Hexo 时博客的 RSS 是全文输出，而换到 Hugo 后 RSS 却变为了输出摘要。作为一个重度 RSS 用户，我自然是深知拉取到的文章还要二次跳转到浏览器才能看原文的体验有多差，所以还是要让 RSS 的表现和此前一致才行。

Hugo 的 RSS 是基于默认 RSS 模板生成的，所以我们只要重新定义一个模板并改为全文输出即可。[Hugo 的默认实现](https://github.com/gohugoio/hugo/blob/master/tpl/tplimpl/embedded/templates/_default/rss.xml)中，决定输出内容的是如下这行代码：

```xml
      <description>{{ .Summary | html }}</description>
```

我们只需要在 `/layouts/` 目录新建 `index.rss.xml` 覆盖默认模版并将原实现拷贝至其中，接着把代码中的 `{{ .Summary | html }}` 替换为代表全文内容的表达式 `{{ .Content | html }}` 即可：

```xml
      <description>{{ .Content | html }}</description>
```

## even主题迁移

由于此前一直在使用[修改过的 Hexo even 主题](https://easonyang.com/2021/08/01/enhanced-hexo-theme-even/)，为保证前端效果不变，所以主题方面也采用了 Hugo 下的 even 主题，因此会有很多主题层面的额外适配工作。

### 自定义导航栏

此前使用 Hexo 下 even 主题时自定义了一个用于引导用户的导航栏，那么如何在 Hugo 的 even 主题中实现兼容呢？

首先我们在 `/layouts/partials/header/` 下新建 `top-nav.html` 模板，按需填充内容，如增加 Newsletter 、Telegram Channel 等引导：

```html
{{- if .Site.Params.enableTopNav }}
  <div class="top-nav">
    {{- if .Site.Params.revue.enabled -}}
      <a href="{{ .Site.Params.revue.home }}" href="_blank" class="top-nav-button">Newsletter</a>
    {{- end -}}
    {{- if .Site.Params.telegram.enabled -}}
      <a href="{{ .Site.Params.telegram.link }}" href="_blank" class="top-nav-button">电报频道</a>
    {{- end -}}
    {{- if .Site.Params.wxOfficialAccount.enabled -}}
      <a href="{{ .Site.Params.wxOfficialAccount.url }}" href="_blank" class="top-nav-button">微信公众号</a>
    {{- end -}}
  </div>
{{- end -}}
```

随后我们需要找个位置引入该模板。受 even 主题实现的限制，我们需要将该模板放置于 header 块之后才能最低成本地保留原布局。因此我们只有一个选择，那就是将 `baseof.html` 这个基础模板进行覆盖。拷贝原模板内容至 `/layouts/_default/baseof.html` 中，并在 `header` 块之后、`main` 块之前引入此前定义的 `top-nav.html` ：

```html
...
  <div class="container" id="mobile-panel">
    {{ if not .Params.hideHeaderAndFooter -}}
    <header id="header" class="header">
        {{ partial "header.html" . }}
    </header>
    {{- end }}
  
    {{- partial "header/top-nav.html" . -}}

    <main id="main" class="main">
...
```

最后在 `config.toml` 中完成相关参数配置即可：

```toml
  [params.wxOfficialAccount]
    enabled = true
    url = ""

  [params.telegram]
    enabled = true
    link = ""
    
  [params.revue]
    enabled = true
    home = ""
```

### 支持umami访问统计

博客之前一直按「[使用Nginx将请求转发至Google Analytics实现后端统计](https://easonyang.com/2016/11/04/google-analytics-via-nginx/)」一文的方式来实现请求统计。但这个方式的问题在于，由于不要求加载 JS ，很多非真实流量（主要为 RSS 阅读器的抓取）也会被统计进来。后来看到「[搭建 umami 收集个人网站统计数据](https://reorx.com/blog/deploy-umami-for-personal-website/)」这篇文章，便也用 umami 搭建了一个轻量的统计能力。

even 主题自然没有对 umami 进行原生支持，我们需要做的是先找到一个包含 head 的模版并在 `<head/>` 标签中添加以下内容：

```html
  {{- if (in (slice (getenv "HUGO_ENV") hugo.Environment) "production") | and .Site.Params.umami.enabled -}}
    <script async defer data-website-id="{{ .Site.Params.umami.id }}" src="{{ .Site.Params.umami.js }}"></script>
  {{- end -}}
```

然后在配置中完成定义即可：

```toml
  [params.umami]
    enabled = true
    id = "" # umami 统计 id
    js = "" # umami 的 JS 地址
```

由于 even 主题没有提供可以直接拓展 `<head/>` 标签的模板，我的选择是将代码加到此前不得不重写的 `baseof.html` 中。不得不说，这个实现很丑陋，但成本确实也是最低的。

此外，为了避免本地启动时 umami 将本地请求也进行了统计并将 Referrer 识别为 localhost ，上文的实现中对环境做了判断，即正式生成站点时才会引入 JS 依赖来上报 umami ，本地运行则不引入。

### 自定义文章末尾页脚

此前在 Hexo 的 even 下我也对文章末尾进行了自定义。对于 Hugo 的 even 主题，改造成本最低的方式为重写 `/layouts/partials/post/copyright.html` 模板。

首先要和此前展现形式对齐的是「原文链接」。even 主题本身只支持将 Markdown 原文件地址作为文章链接，所以我们需要在该模板中仿照 `lionToMarkDown` 部分添加以下内容：

```html
  {{ if $.Site.Params.copyrightLink -}}
    <p class="copyright-item">
      <span class="item-title">文章链接</span>
      <span class="item-content"><a class="link-to-markdown" href="{{ .Permalink }}" target="_blank">{{ .Permalink }}</a></span>
    </p>
  {{- end }}
```

因为暂时没有国际化需要所以文案是固定的中文，如果想更灵活些也可以仿照原实现中的 Markdown link 来做 i18n 。

随后只要在 `copyright.html` 的最后引入我们自定义的文末模板即可：

```html
{{- partial "post/post-footer.html" . -}}
```

### utterances适配

even 主题本身是支持 utterances 的，但用于生成 issue 的唯一标识参数被主题写死为了 `issue-term="pathname"` 即根据 URI 路径生成，并没有暴露配置。而我在使用 Hexo 时该参数的值是 `issueTerm="title"` 即根据文章标题生成，不进行适配的话会丢失存量评论。

所以我们需要在 `/layouts/partials/` 目录下新建 `comments.html` 覆盖主题原实现。顺便地，我们可以把另一个参数 label 也改为可配置的，这样一来，生成的 Github issues 便可以自动加上 utterances 标签方便分类：

```html
{{ if and .IsPage (ne .Params.comment false) -}}

  <!-- utterances -->
  {{- if .Site.Params.utterances.owner}}
    <script src="https://utteranc.es/client.js"
            repo="{{ .Site.Params.utterances.owner }}/{{ .Site.Params.utterances.repo }}"
            issue-term="{{ .Site.Params.utterances.issueTerm }}"
            label="{{ .Site.Params.utterances.label }}"
            theme="github-light"
            crossorigin="anonymous"
            async>
    </script>
    <noscript>Please enable JavaScript to view the <a href="https://github.com/utterance">comments powered by utterances.</a></noscript>
  {{- end }}

{{- end }}
```

随后我们便可以在 `config.toml` 中对 utterances 按需进行配置：

```toml
  [params.utterances]       # https://utteranc.es/
    owner = ""              # Your GitHub ID
    repo = ""               # The repo to store comments
    issueTerm = "title"     # 新增配置，可按需选择 issue 生成时的唯一标识方式
    label = "utterances"    # 新增配置，可按需指定 issue label
```

### 补齐底部社交图标

主题的社交图标使用的是托管于 iconfont 的私有实现所以直接拓展未支持的新图标较为困难。我在[记hexo-theme-even主题优化](https://easonyang.com/2021/07/06/a-better-hexo-theme-even/)一文中提到了相同的问题，文中最终选择了使用 Font Awesome 来解决，对于 Hugo 的 even 主题我们也如法炮制进行处理。

首先，在 [Font Awesome](https://fontawesome.com/start) 官网下载依赖并放置于 `/static` 目录下。例如我使用的是引入所有图标 JS 的方式，则最终路径为 `/static/js/fontawesome.all.min.js` 。然后在 `config.toml` 配置中引入该 JS 文件：

```toml
[params]
  customJS = ["fontawesome.all.min.js"]
```

接着，我们在 `/layouts/partials/` 目录下新建 `footer.html` 覆盖主题原实现并保留原实现的其他代码，只对 `social-links` 部分进行如下修改：

```html
<div class="social-links">
  {{- range $name, $config := .Site.Params.social }}
    {{- if $config.path }}
      <a href="{{ $config.path | safeURL }}" class="iconfont" title="{{ $name }}"><i class="{{ $config.icon }}"></i></a>
    {{- end }}
  {{- end }}
  {{ if .Site.LanguagePrefix -}}
    <a href="{{ .Site.LanguagePrefix | absURL }}/index.xml" type="application/rss+xml" class="iconfont" title="rss"><i class="fas fa-rss"></i></a>
  {{- else -}}
    <a href="{{ .Site.RSSLink }}" type="application/rss+xml" class="iconfont" title="rss"><i class="fas fa-rss"></i></a>
  {{- end }}
</div>
```

最后在 `config.toml` 中添加需要的图标配置即可。`icon` 即图标的完整 `class` 属性，`path` 即需要跳转的链接地址。需要注意的是，主题的原逻辑为了实现多语言，将 RSS 图标的逻辑隔离在了通用逻辑之外。这里也保留了原实现，即 RSS 图标是默认出现且不可去除的。如果不需要 RSS 则可以对上面的代码再进行修改，以删除独立的 RSS 逻辑。

```toml
  [params.social]
    a-email = { title = "Email", icon = "fas fa-envelope", path = "" }
    b-twitter = { title = "Twitter", icon = "fab fa-twitter", path = "" }
    c-github = { title = "Github", icon = "fab fa-github", path = "" }
    d-weixinOfficialAccount = { title = "微信公众号", icon = "fab fa-weixin", path = "" }
    e-telegram = { title = "Telegram", icon = "fab fa-telegram", path = "" }
    f-search = { title = "Search", icon = "fas fa-search", path = "" }
```

除了 Font Awesome ，最近我还看到了[tabler ICONS](https://tabler-icons.io/)这个库，直接支持 SVG 同时还是 MIT 协议的开源项目，也值得一试。

此外，由于我们已经覆盖了 footer 模板，那我们也可以对其他内容也进行自定义，比如将友情链接放置于 footer 等，下文的总字数统计也同样均基于自定义的 `footer.html` 进行处理。

### 支持总字数统计

even 主题自带每篇文章的字数和预计阅读时间统计，但却没有之前我借助 [hexo-wordcount](https://github.com/willin/hexo-wordcount) 所实现的全站文章字数统计。检索网络后找到了这么一篇文章 [Hugo 总文章数和总字数](https://immmmm.com/hugo-total-count/) ，照猫画虎在 `footer.html` 中添加以下内容：

```html
  {{ if .Site.Params.countAllWords.enabled }}
    {{$scratch := newScratch}}
    {{ range (where .Site.Pages "Kind" "page" )}}
    {{$scratch.Add "total" .WordCount}}
    {{ end }}

    <span style="display: block;">
      {{ .Site.Params.countAllWords.prefix }} {{$scratch.Get "total" }} {{ .Site.Params.countAllWords.suffix }}
    </span>
  {{ end }}
```

随后在 `config.toml` 中对相关参数进行配置即可：

```toml
  [params.countAllWords]
    enabled = true
    prefix = "共计"
    suffix = "字"
```

除了以上逻辑，还可以通过改变 `range` 的查询范围来按需限定需要进行总字数统计的页面集合。另外配置中的固定文案比较生硬，可以考虑加入 i18n 相关实现来满足多语言切换的需要。

## 总结

天下武功，唯快不破，Hugo 的速度确实让我印象深刻。但对于从 Hexo 迁移而来，同时还对 Hexo 有很多自定义配置的用户来说，迁移过程中的兼容和适配的成本其实是不低的，实际上目前的迁移仍未实现「基于 LeanCloud 的阅读计数」和「推荐阅读」两项功能的兼容。此外，无论是 Hexo 还是 Hugo ，其主题的深度自定义修改都比较麻烦，以后还是要考虑自己实现一套主题（明年一定）。