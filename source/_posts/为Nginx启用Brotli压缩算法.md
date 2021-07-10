---
title: 为Nginx启用Brotli压缩算法
alias: enable-brotli-for-nginx
tags:
  - Nginx
  - HTTPS
categories:
  - 教程
keywords:
  - Brotli
  - nginx压缩算法
  - 节省流量
  - 教程
date: 2016-12-02 00:05:52
updated: 2016-12-02 00:05:52
---

## 什么是 Brotli 压缩算法

> Brotli最初发布于2015年，用于[网络字体](https://zh.wikipedia.org/wiki/Web%E9%96%8B%E6%94%BE%E5%AD%97%E5%9E%8B%E6%A0%BC%E5%BC%8F)的离线压缩。Google软件工程师在2015年9月发布了包含通用[无损数据压缩](https://zh.wikipedia.org/wiki/%E6%97%A0%E6%8D%9F%E6%95%B0%E6%8D%AE%E5%8E%8B%E7%BC%A9)的Brotli增强版本，特别侧重于[HTTP压缩](https://zh.wikipedia.org/wiki/HTTP%E5%8E%8B%E7%BC%A9)。其中的编码器被部分改写以提高压缩比，编码器和解码器都提高了速度，流式API已被改进，增加更多压缩质量级别。新版本还展现了跨平台的性能改进，以及减少解码所需的内存。
>
> 与常见的通用压缩算法不同，Brotli使用一个预定义的120[千字节](https://zh.wikipedia.org/wiki/%E5%8D%83%E5%AD%97%E8%8A%82)字典。该字典包含超过13000个常用单词、短语和其他子字符串，这些来自一个文本和HTML文档的大型[语料库](https://zh.wikipedia.org/wiki/%E8%AF%AD%E6%96%99%E5%BA%93)。预定义的算法可以提升较小文件的压缩密度。
>
> 使用brotli替换[deflate](https://zh.wikipedia.org/wiki/DEFLATE)来对[文本文件](https://zh.wikipedia.org/wiki/%E6%96%87%E6%9C%AC%E6%96%87%E4%BB%B6)压缩通常可以增加20%的压缩密度，而压缩与解压缩速度则大致不变。使用Brotli进行流压缩的[内容编码类型](https://zh.wikipedia.org/wiki/HTTP%E5%8E%8B%E7%BC%A9)已被提议使用“br”。
>
> 摘自：https://zh.wikipedia.org/wiki/Brotli

另附 Brotli 算法和其他算法的性能比较：

- https://cran.r-project.org/web/packages/brotli/vignettes/benchmarks.html
- https://hacks.mozilla.org/2015/11/better-than-gzip-compression-with-brotli/

## 兼容性

> - Google Chrome supported Brotli from version 49.
> - Microsoft Edge has Brotli in Development.
> - Mozilla Firefox implemented Brotli in version 44. 
> - Opera supports Brotli since version 36.
> - Safari, no public commitment as of October 2016.
>
> 摘自：https://en.wikipedia.org/wiki/Brotli

## 安装 Brotli 所需模块

### 首先安装 [bagder/libbrotli](https://github.com/bagder/libbrotli)<!--more--> 

在 Shell 中依次执行如下命令：

```shell
git clone https://github.com/bagder/libbrotli
cd libbrotli

./autogen.sh
./configure
make && make install
```

**注意：上面安装 Brotli 的方法只适用于  [bfd2885](https://github.com/google/ngx_brotli/commit/bfd2885b2da4d763fed18f49216bb935223cd34b) 这个 commit 前的 ngx_brotli ，对于此后的版本，请直接使用下文的安装方法**

### 添加 [google/ngx_brotli](https://github.com/google/ngx_brotli) 模块并编译安装 Nginx

将 ngx_brotli 模块 clone 到本地：

```shell
git clone https://github.com/google/ngx_brotli
```

**更新：对于 [bfd2885](https://github.com/google/ngx_brotli/commit/bfd2885b2da4d763fed18f49216bb935223cd34b) 后的 ngx_brotli 模块，需要在指定目录下直接克隆相应依赖而不是使用上文的 libbrotli ：** `cd /path/to/ngx_brotli && git submodule update --init` 

然后在 Nginx 的 ./configure 参数后添加 `--add-module=/path/to/ngx_brotli` 并编译安装，示例如下：

```shell
./configure --user=nginx --group=nginx --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-http_auth_request_module --with-http_xslt_module=dynamic --with-http_image_filter_module=dynamic --with-http_geoip_module=dynamic --with-http_perl_module=dynamic --with-threads --with-stream --with-stream_ssl_module --with-http_slice_module --with-mail --with-mail_ssl_module --with-file-aio --with-ipv6 --with-http_v2_module --with-openssl=/usr/bin/openssl-1.0.2 --add-module=/usr/lib64/nginx/modules/ngx_http_google_filter_module --add-module=/usr/lib64/nginx/modules/ngx_http_substitutions_filter_module --add-module=/usr/lib64/nginx/modules/echo-nginx-module --add-module=/usr/lib64/nginx/modules/ngx_cache_purge --add-module=/usr/lib64/nginx/modules/nginx-ct --add-module=/usr/lib64/nginx/modules/ngx_brotli --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -mtune=generic -Wno-deprecated-declarations'

make && make install
```

## 在 Nginx 配置文件中开启 Brotli 算法

编辑 nginx 配置文件，在 http 块中添加如下内容：

```nginx
brotli               on;  
brotli_comp_level    6;  
brotli_buffers       16 8k;  
brotli_min_length    20;  
brotli_types         *;
```

最后 `nginx -t` 测试没有问题后，执行 `nginx -s reload` 载入新配置，成功开启 Brotli 算法。

## 问题及解决

### 1. Nginx 编译过程报错

通常错误形式类似于：

```shell
...ngx_http_brotli_filter_module.cc:173:1: error: deprecated conversion from string constant to ‘char*’ [-Werror=write-strings]...
```

这种情况下，只要在编译 Nginx 时的 `./configure` 参数 `--with-cc-opt` 中加上 `-Wno-deprecated-declarations` ，即可解决问题。

### 2. 编译成功后重启或重载 Nginx 时提示 `nginx: [emerg] unknown directive "brotli"`

同上

### 3. 编译成功后重启或重载 Nginx 时提示 `error while loading shared libraries: libbrotlienc.so.1`

首先我们尝试为 libbrotlienc.so.1 添加一个软链：

```shell
ln -s /usr/local/lib/libbrotlienc.so.1 /lib64 #AMD64
```

```shell
ln -s /usr/local/lib/libbrotlienc.so.1 /lib #i386
```

再尝试重启 Nginx 。如果仍然报错，再执行 `ldconfig` 命令尝试解决。

### 4. 安装 libbrotli 时报错

通常都是缺少环境导致的，尝试安装以下程序解决：

```shell
#rhel and CentOS
yum install autoconf automake libtool
#debian and ubuntu
apt-get install autoconf automake libtool
```

## 测试

- https://tools.keycdn.com/brotli-test
- 用浏览器或抓包查看请求头是否包含 `Content-Encoding: br`

注：对于本站来说，由于主站采用了较为严格的访客过滤规则，很难直接利用这些工具进行测试。开放 https://test.easonyang.com 供测试使用。