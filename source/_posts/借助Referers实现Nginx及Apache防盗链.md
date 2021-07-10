---
title: 借助Referers实现Nginx及Apache防盗链
alias: use-referers-to-forbid-hotlinking-within-nginx-and-apache
date: 2016-09-10 16:53:32
updated: 2016-09-10 16:53:32
tags: [Nginx, Apache, Linux]
categories: [运维]
keywords: [nginx, apache, 防盗链, referers, nginx-accesskey]
---

## Nginx防盗链

图片外链作为小网站的流量杀手不禁是不行的。在使用 Nginx 的情况下，我们可以在配置文件中定义一个 location ，用正则表达式匹配常用的图片文件后缀，随后的重点在于如何过滤掉非法访客。

### 方案一

```nginx
    location  ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        access_log   off;
        expires      1d;
        valid_referers none blocked *.easonyang.com server_names ~\.google\. ~\.baidu\. ~\.bing\. ~\.so\. ~\.sogou\.;
        if ($invalid_referer) {
            rewrite ^/ https://ooo.0o0.ooo/2016/09/10/57d379576fee1.jpg;
        }
        root /var/www/hexo;                   
    }
```

第四行的 valid_referers 顾名思义，指的是对合法来源的定义，随后的值含义如下（翻译自：[Nginx Docs](https://nginx.org/en/docs/http/ngx_http_referer_module.html)）：<!--more-->

- none ：请求头中来源为空
- blocked ：请求头中有来源，但其内容被防火墙或代理代理服务器删除。这样的值不能以 “`http://`” or “`https://`”开头
- `*.easonyang.com` ：任意的字符串，可以使用 `*` 通配符，这里我指定为本站所有来源
- server_names 后面接 `~\.google\. ` 等：正则匹配常用搜索引擎域名，以放行搜索引擎的爬虫。除了搜索引擎，也可以按需添加。

随后用 Nginx 的 if 判断当前请求来源是否合法，不合法则将所访问的内容重写到一个403图片中。此处需要注意的是这个图片最好放在站外的图床中，比如上面这张就放在了 [sm.ms](https://sm.ms) 中，这样做并不是为了节省流量，而是为了避免请求403图片时又来到了如上的验证中，出现循环验证与重写。如果不使用外部图床，其实还有两个方案：

### 方案二

不重写到403图片，而是直接返回404或403：

```nginx
    location  ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        access_log   off;
        expires      1d;
        valid_referers none blocked *.easonyang.com server_names ~\.google\. ~\.baidu\. ~\.bing\. ~\.so\. ~\.sogou\.;
        if ($invalid_referer) {
            #return 404;
    		return 403;
        }
        root /var/www/hexo;                   
    }
```

### 方案三

为403图片单独定义一个 location （两个 location 的顺序不能颠倒）：

```nginx
    location ~ ^/403\.jpg$ {
        root    /var/www;
    }

    location  ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        access_log   off;
        expires      1d;
        valid_referers none blocked *.easonyang.com server_names ~\.google\. ~\.baidu\. ~\.bing\. ~\.so\. ~\.sogou\.;
        if ($invalid_referer) {
            rewrite ^/ https://easonyang.com/403.jpg;
#            rewrite ^/ https://ooo.0o0.ooo/2016/09/10/57d379576fee1.jpg;
        }
        root /var/www/hexo;                   
    }
```

### 注意事项

不要对再定义其他 location 来复写图片相关的格式的匹配，否则不能保证实现本需求。

## Apache防盗链

对于 Apache 来说，自然就要用到 .htaccess 了，实现原理与 Nginx 是类似的。借助于 [htaccesstools.com](http://www.htaccesstools.com/hotlink-protection/) 这个非常实用的网站，我们只需保证 mod_rewrite 已开启后（终端中执行 `a2enmod rewrite` 命令开启 mod_rewrite ），在这个网站上按需选择生成 .htaccess 后拷贝到服务器上的 .htaccess 中即可。

## 后记

这种使用 referer 过滤非法访客的方法简单实用，能防止所有直接复制图片链接到自己网站进行使用的盗链方式，但是由于 referer 是从请求头中获取的，修改请求头是件再容易不过的事情了，因此只能说无法防住更高级的盗链，同时对爬虫也是束手无策。这时就要用些相对高级的方法，例如对不同的请求生成不同的 key ，从而杜绝非法请求，Nginx 有一个现成的模块实现了这种方式：[nginx-accesskey](http://wiki.nginx.org/images/5/51/Nginx-accesskey-2.0.3.tar.gz) （此模块似乎已经很久没有更新过了，只找到了几年前的2.0.3版）。