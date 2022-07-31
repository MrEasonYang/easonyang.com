---
title: Podistributor播客分发系统介绍
url: 2021/02/19/podistributor-cn-readme
date: 2021-02-19 13:27:02
updated: 2021-02-19 13:27:02
tags: [podcast, open-source]
categories: [programming]
keywords: [播客, podistributor, 分发, golang, github]
---
一个基于 Golang 的播客分发程序。
Github Repo：[MrEasonYang/podistributor](https://github.com/MrEasonYang/podistributor)

## 特性

- 向用户暴露节目的别名 URL ，在用户访问时重定向至真实的目标资源 URL ，以高效地进行 CDN 切换和便捷地建立失效转移机制。
- 异步转发请求至统计服务，以解耦用户请求和数据统计，可方便地接入多个数据统计服务或替换失效的统计服务。
- 内建针对数据库的本地缓存层以提供高性能的服务并降低攻击流量带来的影响。
- 内建基于 Prometheus 的监控和统计系统。
<!--more-->

## 快速开始

### 环境要求

- MySQL: 用于播客和节目数据持久存储。
- Golang: 用于编译项目。
- Nginx(推荐): 反向代理原始的 podistributor 服务，以加入限流、HTTPS 等机制。
- Prometheus(可选的): 结合内建的数据统计埋点对系统进行监控和统计。

### 安装
首先按照 [SQL example](https://github.com/MrEasonYang/podistributor/blob/main/podistributor.sql) 将数据库和数据表结构导入 MySQL 实例中。

随后使用 Golang 编译源码:

```shell
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o podistributor main.go
chmod 773 podistributor
./podistributor -decryptKey <AES 加密密钥> -configLocation <配置文件的目录地址>
```

`decryptKey` 和 `configLocation` 为必传参数，其他参数可参照 [config file example](https://github.com/MrEasonYang/podistributor/blob/main/podistributor-config.yaml) 配置于文件中。
除了独立运行程序，还可以借助 systemd 开启守护进程以作为服务运行。可在 `/usr/lib/systemd/system/` 添加如下配置并保存为 `podistributor.service` ：

```
[Unit]
Description=podistributor
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/sbin/podistributor/podistributor -decryptKey <AES 加密密钥> -configLocation <配置文件的目录地址>
StandardOutput=append:/var/log/podistributor.log
StandardError=append:/var/log/podistributor.log
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=always

[Install]
WantedBy=multi-user.target
```

接着我们就可以将程序作为 systemd 的服务来运行并支持开机自启动：

```shell
systemctl enable podistributor
systemctl start podistributor
```

### 使用

#### 访问播客数据

如下格式的请求将会被解析并重定向至真实资源地址：

```
<protocal http or https>://<domain or ip>:<Optional listen port if using nginx>/<listenPath>/<podcast unique name>/ep/<episode unique name>
```

系统将根据播客 DB 配置中的等级字段来在资源 uri 集合中确定重定向的目标 url ，此等级即可理解为资源集合数组的数组索引。

此外，若主 uri 资源列表均不可用，可将播客 DB 配置中的备用 url 标识字段设置为 true ，系统即将根据备用资源等级字段来在备用资源 uri 集合中确定重定向的目标 url 。同理，这里的备用资源等级字段亦为备用资源 uri 集合数组的数组索引。

若配置了统计 URL 列表，则所有的统计 URL 都将被异步请求，请求是否成功均不影响原始请求。

#### 访问统计信息
Podistributor 借助 Prometheus 的 client_golang 进行指标统计并将数据以 HTTP 接口的形式向外暴露，默认 HTTP 端口号为 `11800` ，可在 `podistributor-confi.yaml` 修改 `monitorPort` 的值以指定其他端口。

```
curl http://127.0.0.1:11800/metrics
```

对于 Prometheus 服务端，只需在配置文件 `/etc/prometheus/prometheus.yml` 中新增数据拉取 job 即可：

```
...
    - job_name: 'podistributor'
      static_configs:
      - targets: ['127.0.0.1:11800']
        labels:
          instance: pod-instance
...
``` 

## 授权协议

[Apache License 2.0](https://github.com/MrEasonYang/podistributor/blob/main/LICENSE)