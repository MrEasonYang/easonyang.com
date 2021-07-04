---
title: 使用Nginx反代Google Fonts
date: 2016-08-06 23:33:55
updated: 2016-08-06 23:33:55
tags: [Nginx, Hexo]
categories: [教程]
keywords: [Nginx, Hexo, google fonts, proxy, 反代]
---

## 前言

<del> 虽然 360 有免费的 Google Fonts 等的代理服务，但却只支持 HTTP ，对于使用 HTTPS 的站点来说使用还是有些麻烦的，而且这个 CDN 在不同网络下似乎有些不太稳定。</del> 360 的免费 CDN 已经迁移到了 https://cdn.baomitu.com/ ，支持 HTTPS 和 HTTP2 ，经测试国内国外访问速度都很好。而为了解决本站的字体问题，果断使用 Nginx 反代 Google Fonts。Nginx 配置文件如下。

## 反代 fonts.googleapis.com

```nginx
server {
	listen       80;
	listen       [::]:80;
	server_name  fonts.easonyang.com;

	location ^~ /.well-known/acme-challenge/ {
		default_type "text/plain";
		root /var/www/letsencrypt;
	}
	return 301 https://$server_name$request_uri;
}

server {
	listen       443 ssl;
	listen       [::]:443 ssl;
	server_name  fonts.easonyang.com;
	ssl_certificate      /etc/lets-encrypt/easonyang/easonyang.chained.crt;
	ssl_certificate_key  /etc/lets-encrypt/easonyang/easonyang.com.key;
	ssl_session_cache    shared:SSL:1m;
	ssl_session_timeout  5m;

	ssl_ciphers  HIGH:!aNULL:!MD5;
	ssl_prefer_server_ciphers  on;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

	location / {
		proxy_pass https://fonts.googleapis.com;
		proxy_buffering off;

		proxy_cookie_domain fonts.googleapis.com fonts.easonyang.com;
		proxy_redirect https://fonts.googleapis.com/ /;

		proxy_set_header X-Real_IP $remote_addr;
		proxy_set_header User-Agent $http_user_agent;
		proxy_set_header Accept-Encoding ''; 
		proxy_set_header referer "https://fonts.googleapis.com$request_uri";

		subs_filter_types text/css text/xml text/javascript;
		subs_filter fonts.googleapis.com fonts.easonyang.com;
	}

	location ^~ /.well-known/acme-challenge/ {
		default_type "text/plain";
		root /var/www/letsencrypt;
	}
}
```
## 反代 fonts.gstatic.com

```nginx
server {
	listen       80;
	listen       [::]:80;
	server_name  gstatic.easonyang.com;

	location ^~ /.well-known/acme-challenge/ {
		default_type "text/plain";
		root /var/www/letsencrypt;
	}
	return 301 https://$server_name$request_uri;
}

server {
	listen       443 ssl;
	listen       [::]:443 ssl;
	server_name  gstatic.easonyang.com;
	ssl_certificate      /etc/lets-encrypt/easonyang/easonyang.chained.crt;
	ssl_certificate_key  /etc/lets-encrypt/easonyang/easonyang.com.key;
	ssl_session_cache    shared:SSL:1m;
	ssl_session_timeout  5m;

	ssl_ciphers  HIGH:!aNULL:!MD5;
	ssl_prefer_server_ciphers  on;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

	location / {
		proxy_pass https://fonts.gstatic.com;
		proxy_buffering off;

		proxy_cookie_domain fonts.gstatic.com gstatic.easonyang.com;
		proxy_redirect https://fonts.gstatic.com/ /;

		proxy_set_header X-Real_IP $remote_addr;
		proxy_set_header User-Agent $http_user_agent;
		proxy_set_header Accept-Encoding ''; 
		proxy_set_header referer "https://fonts.gstatic.com$request_uri";

		subs_filter_types text/css text/xml text/javascript;
		subs_filter fonts.gstatic.com gstatic.easonyang.com;
	}

	location ^~ /.well-known/acme-challenge/ {
		default_type "text/plain";
		root /var/www/letsencrypt;
	}
}
```