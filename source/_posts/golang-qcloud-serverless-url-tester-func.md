---
title: 基于腾讯云Serverless的HTTP服务探活函数
date: 2021-02-21 21:57:38
updated: 2021-02-21 21:57:38
tags: [工具, 开源]
categories: [编程]
keywords: [serverless, 腾讯云, telegram, telegram bot, golang, 弹活, 拨测]
---
本文基于 Golang 开发了一款简单易用的拨测云函数，入口函数与腾讯云 Serverless SDK 绑定。与目前腾讯云中默认的拨测函数不同的是， url-tester-func 支持非 200 响应码作为预期值且通知机制由邮件变更为了 Telegram Bot 。使用者借助腾讯云提供的免费 Serverless 调用配额即可搭建一套简单的 HTTP 接口探活服务。
### 项目地址
[MrEasonYang/url-tester-func](https://github.com/MrEasonYang/url-tester-func)
### 使用
1. 首先需要根据腾讯云文档创建 Serverless 函数的配置文件, 可参照如下示例配合文档进行配置: [serverless.yaml](https://github.com/MrEasonYang/url-tester-func/blob/main/serverless.yaml.example)

2. 本地安装 Golang 并构建项目:

   ```shell
   CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build main.go
   ```
<!--more-->

3. 程序依赖腾讯云中的全局变量配置来获取目标 URL 集合配置和 Telegram API 配置，在腾讯云的管理平台中按如下格式设置即可：
   - **config**: [{"URL": "<目标 URL 1>","ExpectedStatusCode": 200},{"URL": "<目标 URL 2>","ExpectedStatusCode": 403}]
   - **token**: <Telegram 机器人的 API Token ，在创建机器人时 BotFather 会直接以消息形式返回>
   - **chatID**: <消息发送的目标聊天（群），必须已经与 Telegram 机器人绑定，可参照 Telegram 文档从其 updates 接口中获取>

4. 在腾讯云上新建一个 Serverless 函数，随后上传包含 `serverless.yaml` 和构建后函数的文件至腾讯云平台，创建并启用定时器触发器后程序即可正常运行。当目标服务按预期正常响应时程序将在每次执行后返回成功 JSON 结果，否则程序将借由绑定的 Telegram Bot 发送如下格式的消息至目标聊天中，消息内容将包含拨测失败的错误内容：

```
Failed to test URL https://foo.foo due to error Get "https://foo.foo": dial tcp: lookup foo.foo on 10.10.10.10:53: read udp 10.10.10.10:5180->10.10.10.10:53: i/o timeout
```