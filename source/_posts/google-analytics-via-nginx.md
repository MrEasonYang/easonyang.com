---
title: 使用Nginx将请求转发至Google Analytics实现后端统计
date: 2016-11-04 13:09:24
updated: 2016-11-04 13:09:24
tags: [Nginx, 工具]
categories: [教程]
keywords: [Nginx, Google Analytics, Google分析, 请求转发, 教程]
---

## 前言

Google Analytics 加载缓慢是本博客在国内访问缓慢的原因之一。虽然通过使用大公司的 ga.js 的 CDN ，可以很大程度上加快加载 ga.js 文件的速度（ ga.js 的更新频率很高，并不适合缓存到本地或服务器，所以最好自己反代或者借大公司的光，参见 [谷歌分析(Google Analytics) 加速实现秒加载页面](https://joway.wang/posts/Hexo/2016-05-15-Google-Analytics.html) ），但无论如何还是要向 Google 发送一个请求来发送访客数据，所以取巧地使用 CDN 并不能从根本上解决问题。前段时间翻看一个知名博主的文章时，有一篇名为 [本博客零散优化点汇总](https://imququ.com/post/summary-of-my-blog-optimization.html) 的文章中写道：「通过服务器端异步发送统计数据的方式来解决这个问题」，这的确是个经济实惠的办法，下面就来实践一下。

## 思路

- 方案一：通过 Nginx 的 proxy ，根据 [官方给出的文档](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference) 向 Google 转发访客数据。
- 方案二：设立中转服务器，在客户端通过 JS 发送异步请求给中转服务器，由中转服务器将访客信息发送给 Google。

这两种方法都切实可行，不过也都有一个大前提，那就是服务器（中转服务器）必须设置于境外，能够顺畅地访问 Google Analytics ，否则做的也不过是无用功。本文选择较为简单但是定制性不是很强的第一种方案，第二种方案可以参考这篇博文及博主开源的项目：[服务端使用 Google Analytics](https://blog.alphatr.com/google-analytics-on-server.html) 。<!--more-->

## 建立访客 ID

如果我们查看使用 Google Analytics 服务的网站，我们总会在看到一个 _ga 的 cookie 。实际上， ga.js 将此 cookie 中的值做处理后作为本次访问访客的 uid 传回 Google 的服务器，Google Analytics 根据这个 uid 进行访客行为的追踪和新旧访客的统计，这也是官方文档中 cid 参数的作用所在。

因此我们必须针对每一个用户手动生成一个 uid 发送给 Google ，同时也要将这个 uid 保存在客户端的 cookie 中，从而实现在每次访问中，通过判断该 cookie 是否为空来决定是否生成新的 uid 。

本文借助 Nginx 的 [userid](http://nginx.org/en/docs/http/ngx_http_userid_module.html) 模块，便可很轻松的实现这一功能。不过如果想要实现更完善的 uid 生成，还是手写模块或者写 lua 脚本比较好。在 nginx 配置文件中添加如下内容来开启 userid 模块：

```nginx
    userid on;
    userid_name cid;
    userid_domain easonyang.com;
    userid_path /;
    userid_expires max;
```

## 建立 tracker location

要想使用 Nginx 发送异步请求，就要活用 nginx 的「 proxy 」和「 location 内部 redirect 匹配」两大功能。这一节内容部分借鉴于 [Ng­inx 内配置 Google An­a­lyt­ics 指南](https://darknode.in/network/nginx-google-analytics/) 一文。

我们知道「 location 内部 redirect 匹配」的写法是这样的：

```nginx
location @tracker {
}
```

所以，我们只要在上面这个 location 块中添加 proxy_pass 就可以了。此处需要注意的是，如果按照上文提到的那篇文章进行配置，我们会发现在大部分情况下转发总是不成功，经过研究，这种情况下必须要加上一个能解析 Google 的 DNS 才可以成功转发。这里直接使用经典的 8.8.8.8 及其 IPV6 地址，最后 location 块为： 

```nginx
location @tracker {        
        internal;
        resolver 8.8.8.8 [2001:4860:4860::8888];
        proxy_method GET;
        proxy_pass https://www.google-analytics.com/collect?v=1&tid=UA-11111111-1&$uid_set$uid_got&t=pageview&dh=$host&dp=$uri&uip=$remote_addr&dr=$http_referer&z=$msec;
        proxy_set_header User-Agent $http_user_agent;
        proxy_pass_request_headers off;
        proxy_pass_request_body off;
}
```

## 使用 tracker location

最后只要在控制网站访问的 location 块中加上 `post_action @tracker;` 就大功告成了。示例：

```nginx
    location / {
        root /var/www/hexo;
        post_action @tracker;
    }
```

然而在实际使用中，我们会发现这样配置使得在 Google Analytics 中看到的网站访问量大大增加，这似乎不像是绕过 Adblock 这样插件而实现的效果。实际上，这是源于在上文的配置中，Nginx 直接把各种搜索引擎蜘蛛也当作普通访客发送给 Google Analytics 的缘故。因此还要将 Spider 过滤掉：

```nginx
    location / {
        root /var/www/hexo;
        if ($http_user_agent !~* "qihoobot|Baiduspider|Googlebot|Googlebot-Mobile|Googlebot-Image|Mediapartners-Google|Adsbot-Google|Feedfetcher-Google|Yahoo! Slurp|Yahoo! Slurp China|YoudaoBot|Sosospider|Sogou spider|Sogou web spider|MSNBot|ia_archiver|Tomato Bot|YiSou Spider") {
            post_action @tracker;
        }
    }
```

## 为 Hexo 配置 tracker location

由于本文使用 Hexo 博客系统，所以此处特别说明一下 Hexo 相关的 Nginx 配置。

### 配置

在为 Hexo 配置 Nginx 时，我们总习惯于将 location 定义为：

```nginx
    location / {
        root /var/www/hexo/easonyang.com/blog;                   

        if (-f $request_filename) {                                            
            post_action @tracker;
            rewrite ^/(.*)$  /$1 break;   
        }
    }
```

这样一来我们就要进一步定制 hexo 的 location ：

```nginx
    location / {
        root /var/www/hexo;                   

        set $flag "0";

        if (-f $request_filename) {                                            
            set $flag "${flag}1";
        }   

        if ($http_user_agent !~* "qihoobot|Baiduspider|Googlebot|Googlebot-Mobile|Googlebot-Image|Mediapartners-Google|Adsbot-Google|Feedfetcher-Google|Yahoo! Slurp|Yahoo! Slurp China|YoudaoBot|Sosospider|Sogou spider|Sogou web spider|MSNBot|ia_archiver|Tomato Bot|YiSou Spider") {
            set $flag "${flag}2";
        }

        if ($flag = "012") {
            post_action @tracker;
            rewrite ^/(.*)$  /$1 break;                                    
        }

        if ($flag = "01") {
            rewrite ^/(.*)$  /$1 break;                                    
        }
    }
```

### 说明

> If is devil.

这是 Nginx 的使用者都知道的至理名言，此处逻辑比较简单，就直接用 if 了。非常不喜欢 if 的话就用 map 代替下吧。

## 测试

暂时将 `proxy_pass https://www.google-analytics.com/collect?v=1&tid=UA-11111111-1&$uid_set$uid_got&t=pageview&dh=$host&dp=$uri&uip=$remote_addr&dr=$http_referer&z=$msec;` 修改为 `proxy_pass https://127.0.0.1:9999/collect?v=1&tid=UA-11111111-1&$uid_set$uid_got&t=pageview&dh=$host&dp=$uri&uip=$remote_addr&dr=$http_referer&z=$msec;` ，然后在服务器上运行命令：

```shell
 nc -k -l 0.0.0.0 9999
```

监听 9999 端口，最后访问网站，就可以在服务器上查看到请求的相应信息。

## 关于更进一步的请求过滤

虽然我们可以通过设定更加复杂的 Nginx 配置，来实现过滤特定请求进而避免这些无用数据如 404 页面被发往 Google Analytics 的情况发生，但我们完全可以使用更加方便、高效的 Google Analytics 过滤器来满足各种过滤需求，详细配置参见 Google 的官方文档：

- [https://support.google.com/analytics/answer/1034823?hl=zh-Hans](https://support.google.com/analytics/answer/1034823?hl=zh-Hans)
- [https://support.google.com/analytics/answer/1034380?hl=zh-Hans](https://support.google.com/analytics/answer/1034380?hl=zh-Hans)
- [https://support.google.com/analytics/answer/1034832?hl=zh-Hans](https://support.google.com/analytics/answer/1034832?hl=zh-Hans)
- [http://bluewhale.cc/tag/filter](http://bluewhale.cc/tag/filter)

本文由 [Eason Yang](https://easonyang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://easonyang.com/about/)。