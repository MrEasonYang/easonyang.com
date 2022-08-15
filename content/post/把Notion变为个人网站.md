---
title: "把Notion变为个人网站"
date: 2022-08-15T12:43:11+08:00
lastmod: 2022-08-15T12:43:11+08:00
draft: false
slug: "build-a-website-with-notion"
keywords: ["notion", "个人网站", "fruition", "popsy", "nginx"]
description: ""
tags: ["notion"]
categories: ["course"]
---
「The all-in-one workspace」是 Notion 的自称，它肯定不是最优秀的笔记软件，但一定是当今最强大的内容生产工具之一。除了可玩性很高的数据库功能，Notion 还支持将页面对外公开，即可以通过简单地启用分享来获取到可在互联网上直接访问的链接。不过免费版不支持为被分享页面配置 SEO ，而且即使是付费版也尚未支持自定义域名。

如果想建立一个可高度自定义的个人网站，或希望把 Notion 的页面作为已有网站的附加内容，那就还是要基于 Notion 建立一个拥有独立域名、支持自定义设置的个人网站。本文就来聊聊基于 Notion 的个人网站有几样建法。<!--more-->

## 建站方案

### 基于SaaS产品建站

Notion 如今已经形成了生态，既然官方没提供完整的自定义个人网站方式，那各种 SaaS 产品就会补足市场的需求。当前已经出现了很多这类产品，通常是免费版提供另一个二级域名和自定义主题功能，付费订阅版才有自定义域名、支持 SEO 等高级功能。

这些产品的界面都很简洁，操作也简单易懂。以 [Popsy](https://popsy.co/) 为例，选择新建站点：

![创建 Notion 站点](https://gmiimg.com/c60e4e8f3cfd710423632f9235ada781.png)

最后填入站点名称和 Notion 页面的分享链接即可：

![完成初始化配置](https://gmiimg.com/61cdbc3c1bed6e226c7d5f498732239c.png)

站点新建完成后，我们可以在左侧的导航栏里完成自定义设置，最后点击 Publish 完成发布即可。不过 Popsy 没有免费套餐，需要先在 Stripe 完成支付配置才可以试用 7 天。

SaaS 类产品的操作确实非常简单，功能上也可以满足用户大部分的自定义需求，很适合非技术人员或不愿意折腾的用户。唯一的缺点只有一个字，那就是「贵」，订阅价格通常在每月 $8～12 不等。贴几张不同产品价格功能表的截图，大家感受下：

![Popsy 价格功能表](https://gmiimg.com/79740a302182d0f8cc683d3b1823eb38.png)

![Simple.ink 价格功能表](https://gmiimg.com/ecabfd16b0c395e7e26f7f32ad7c763e.png)

![Potion 价格功能表](https://gmiimg.com/294c7236642e2459a2ccb5dab9263966.png)

### 基于FaaS产品建站

除了付费产品，在流量不大时我们还可以借助 FaaS 产品的免费额度来实现建站。目前比较常见的思路分为 Cloudflare Workers 和 Vercel 两类，希望快速可用建议选 Workers ，打算通过开发来实现自定义的则可以使用 Vercel 方案。

#### 基于Cloudflare Workers建站

这个方案的原理是通过 Workers 的转发和响应重写功能对 Notion 进行代理。由于这套逻辑大同小异，所以有网友开发了 Workers 代码的生成器。可视化界面的简单配置即可生成对应的代码，再拷贝到 Cloudflare Workers 中执行即可。

以最为著名的 [Fruition](https://fruitionsite.com/) 为例，我们首先完成 Step0 和 Step1 中的域名注册、Cloudflare 注册和 DNS 配置，随后在 Step2 中填写好期望的域名和 Notion 页面的分享链接，最后把生成的代码拷贝到新建的 Cloudflare Workers 函数中，再将该域名的路径设置为 Worker 的路由即可。官网的教程非常详细，按指导一步步执行即可。在 Step3 中我们可以做很多自定义工作，而由于该 Worker 的实现主要依赖重写响应，因此我们也可以对生成的代码进行深度修改以满足各种自定义需求。

不过我们从 Fruition 官网教程的截图也能看到，所使用的 Cloudflare 截图已经是改版前的页面了。无独有偶，该教程的细节也存在一些需要与时俱进的调整。在 Step1 的第 4 小节，教程中要求为目标域名添加 A 记录并将 IP 指向 `1.1.1.1` ：

> If you don't have any A records imported, add one with your root domain as the Name and 1.1.1.1 as the Content. Otherwise, click Continue on the DNS Record page.

但实际上如果你真的这样配置，那在当前的 Cloudflare 机制下会出现网页无法打开的问题。原因在于现在 Cloudflare 当前已经禁止了这种 DNS 配置方式，对应的 Workers  需要使用保留 IP 地址（Reserved address）比如 `192.0.2.0` ，CIDR 为  `192.0.2.0/24` ，这其后的技术细节以后可以展开讲讲。

其他基于 Cloudflare Workers 的方案和 Fruition 类似，区别只在于 Worker 中代码实现思路的不同。Cloudflare 作为良心企业，免费版 Worker 每天有 100k 请求的额度，对于绝大部分的基于 Notion 的个人网站来说是完全足够的。即使真的超过了额度，多加 $5 就可以多获得 100 倍的可用请求量，不可谓不划算。

缺点在于网上比较流行的几种 Worker 实现对 SEO 的支持都不算友好，也没有直接支持评论等功能，需要做比较多的二次开发才能满足较为复杂的需求。同时近年来 Cloudflare 的国际 CDN 在国内的访问速度越来越慢，有的地区甚至还会间歇性地无法访问，因此国内用户的使用体验可能会不太好。

#### 基于Vercel建站

既然是折腾和前端相关的东西，怎么能少了 Vercel 呢？比较著名的 Vercel 实现为 [ijjk/notion-blog](https://github.com/ijjk/notion-blog) 项目。如果你熟悉 Vercel ，那基础配置比 Cloudflare Workers 方案要简单得多，只要找到 `NOTION_TOKEN` 和 `BLOG_INDEX_ID ` 两个值（可以参考[文档](https://github.com/ijjk/notion-blog#getting-blog-index-and-token)）就可以一键启动，然后再把实例绑定到期望的域名即可。

其实现机制和上文的 Cloudflare 方案差异较大，本质上是一个以 Notion 为数据库（直接访问 Notion 的内部 API ）的基于 Next.js 的动态博客系统，这也是为什么该项目会要求相关 Notion 页面具备一个类似于其他博客系统的 front matter 配置即 table 用于记录元数据的原因。

虽然部署方式更简单了，但如果想做调整那就要对原项目进行修改。所以整体的自定义复杂度其实还是挺高的，更适合对前端项目有一定积累的用户使用。另外 Vercel 在国内的访问速度没比 Cloudflare 好多少，所以对于国内用户来说同样也可能会有体验问题。

### 自助反向代理建站

如果手上已经有了一台 VPS 之类的机器，那我们还可以选择通过反向代理 Notion 的方式来复用已有机器自助建站。

以 Nginx 为例，我们首先要保证 Nginx 已经启用了 `ngx_http_sub_module` 模块，这个模块不一定是默认开启的，有可能会需要手动编译。该模块的主要作用为按配置修改代理后的响应内容。这一点至关重要，不然我们无法解决 Notion 原始响应中各类固定域名和响应字段，进而也就无法完成反向代理。

下面给出仅考虑可正常加载页面的最简单情况下 Nginx location 的配置，并对重点指令进行解释，自定义域名使用 `example.foo` 指代：

```Nginx
    location / {
        # 由于 Notion 页面经常比较大，默认 buffer 配置容易出现 502 的情况，所以需要调大 buffer ，具体大小可以按需选择
        proxy_buffers 8 32k;
        proxy_buffer_size 64k;
        
        # rewrite 至共享空间的首页页面，以处理根路径的访问
        rewrite ^/$ https://example.foo/Foo-b1324470dd5d33d04;

        # 将流量代理至 Notion ，网上有很多实现的代理目标是 notion.so ，但 Notion 的域名规则已经发生变化，改为了 {用户名}.notion.site ，故此处也要进行修正，下同
        proxy_pass https://example.notion.site;

        # 设置一系列的代理请求头，保证对于 Notion 来说请求仍可信且可处理
        proxy_set_header Host example.notion.site;
        proxy_set_header Referer https://example.notion.site/;
        proxy_set_header User-Agent $http_user_agent;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Accept-Encoding "";
        proxy_set_header Accept-Language $http_accept_language;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # 将响应中的 Notion 域名替换为自定义域名，以保证 XHR 等请求也能被正确地反向代理
        sub_filter 'https://www.notion.so' 'https://example.foo';
        sub_filter 'https://example.notion.site' 'https://example.foo';
        
        # 上述逻辑会导致 Notion 前端代码将 requestedOnPublicDomain 判断为 true 并携带此参数向 Notion 后端请求是否需要登录，最终导致强跳至登录页。由于 Notion 前端实现比较复杂，这里简单处理为对 Notion 后端返回的需要登录字段强制覆盖
        sub_filter '"requireLogin":true' '"requireLogin":false';
        
        # 上述替换涉及到了 application/javascript 和 application/json ，但实际上有些 text/plain 响应也包含了需要替换的内容。所以这里设置为替换所有 MIME 类型的响应
        sub_filter_types *;
        
        # 为 off 时表示需要扫描原始响应中的所有内容进行替换，而不是默认的替换一次即停止
        sub_filter_once off;

        # 反向代理时启用 SNI ，避免 TLS 握手出现问题
        proxy_ssl_server_name on;
    }
```

这类自定义反向代理方案的好处在于能复用已用机器资源，同时如果机器的网络比较优秀，还可以加速对 Notion 的访问，解决了上面几种方案在国内的尴尬使用体验。缺点是过于依赖 Notion 的前后端设计，Notion 如果做了大改动，那实现方式就需要重新探索。其实 Cloudflare Workers 方案也有这个问题，不过作为活跃的开源项目，Fruition 等的社区支持还是比较给力的。

## 总结

笔记工具远没有持续输出重要。对于上述几个方案的选择，我的建议是没特殊需要就使用 Cloudflare Workers 实现，舍得花钱、想省事就选 SaaS 方案，愿意折腾、乐于折腾再考虑 Vercel 或 Nginx 方案。