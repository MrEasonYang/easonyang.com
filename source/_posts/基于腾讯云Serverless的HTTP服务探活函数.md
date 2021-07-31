---
title: 基于腾讯云Serverless的HTTP服务探活函数
alias: golang-qcloud-serverless-url-tester-func
date: 2021-02-21 21:57:38
updated: 2021-02-21 21:57:38
tags: [工具, 开源]
categories: [编程]
keywords: [serverless, 腾讯云, scf, telegram, telegram bot, golang, 探活, HTTP拨测]
---
本文基于 Golang 开发了一款简单易用的 HTTP 拨测云函数，入口函数与腾讯云 Serverless SCF SDK 绑定。与目前腾讯云中默认的拨测函数不同的是， url-tester-func 支持将非 200 响应码作为预期值且通知机制由邮件变更为了 Telegram Bot 。使用者借助腾讯云提供的免费 Serverless 调用配额即可搭建一套简单的 HTTP 接口探活服务。

## 功能
- 周期性探测指定 HTTP 地址是否可正常响应，并将非正常的探测结果发送至指定即时通讯工具的对话中以实现近乎实时地异步监控网站状态
- 支持将 Telegram/Server酱 V1(微信)/Server酱 V2(微信)/Qmsg酱 (QQ，支持单聊及群聊)
- 可识别的包含 Bot 信息的自定义 User-Agent (url-tester-func BOT)
- 基于腾讯云 Serverless SCF ，部署简单且零成本

## 项目地址
[MrEasonYang/url-tester-func](https://github.com/MrEasonYang/url-tester-func)

## 使用
### 填写函数配置
首先需要根据腾讯云文档创建 Serverless 函数的配置文件, 可参照如下示例配合文档进行配置: [serverless.yaml](https://github.com/MrEasonYang/url-tester-func/blob/main/serverless.yaml.example)

### 构建
本地安装 Golang 并构建项目:

   ```shell
   CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build main.go
   ```
<!--more-->

### 本地测试
参考腾讯云文档进行本地测试：[腾讯云SCF说明](https://github.com/MrEasonYang/url-tester-func/blob/main/README-QCLOUD.md)

### 创建函数
#### 在腾讯云中添加函数
在已注册腾讯云账号后，访问[函数管理后台](https://console.cloud.tencent.com/scf/list-create?rid=5&ns=default&createType=empty)选择「自定义创建」来新建一个函数。配置可参考下图，「环境变量」参数在下文中会有详细描述，图中未展示的其他参数保持默认即可：
![函数配置示例](https://gmiimg.com/cdcfc4c991e4e5ebe8cd5967abc2be54.png)
需要注意的是，为了保证可正常访问 Telegram API ，函数的部署地域应选择中国大陆之外的区域。

#### 配置环境变量
程序依赖腾讯云中的全局变量配置来获取目标 URL 集合配置和 Telegram API 配置，在腾讯云的管理平台中按如下格式设置即可：
- **config**: 
配置结构：
  1. url: 测试的目标 URL
	2. expectedStatusCode: 期望 URL 返回的 HTTP 响应码，除此之外的相应码均将触发失败消息提醒。
	3. notifyMethod: 通知方式，应设置为 telegram/ftqq_v1/ftqq_v2/qmsg_chat/qmsg_group_chat 中的某一个。


示例:
```json
[
    {
        "url": "<Target URL 1 with Telegram Notifier>",
        "expectedStatusCode": 200,
        "notifyMethod": "telegram"
    },
    {
        "url": "<Target URL 2 with FtqqV1 Notifier>",
        "expectedStatusCode": 403,
        "notifyMethod": "ftqq_v1"
    },
    {
        "url": "<Target URL 3 with FtqqV2 Notifier>",
        "expectedStatusCode": 400,
        "notifyMethod": "ftqq_v2"
    },
    {
        "url": "<Target URL 4 with Qmsg Notifier>",
        "expectedStatusCode": 301,
        "notifyMethod": "qmsg_chat"
    },
    {
        "url": "<Target URL 4 with Qmsg Group Notifier>",
        "expectedStatusCode": 200,
        "notifyMethod": "qmsg_group_chat"
    },
]
```
- **telegram_token(使用 Telegram 时必填)**: Telegram 机器人的 API Token ，在创建机器人时 BotFather 会直接以消息形式返回，可参考官方 [FAQ](https://telegra.ph/Awesome-Telegram-Bot-11-11) 来获取
- **telegram_chat_id(使用 Telegram 时必填)**: 消息发送的目标聊天（群），必须已经与 Telegram 机器人绑定，可参照 Telegram 文档从其 updates 接口中获取，可参考[此文](https://stackoverflow.com/questions/32423837/telegram-bot-how-to-get-a-group-chat-id)获取
- **qmsg_key(使用 Qmsg酱时必填)**: [Qmsg酱 key](https://qmsg.zendee.cn/me.html)
- **ftqq_v1_key(使用 V1 版本 Server酱时必填)**: [Server酱 V1 send key](https://sct.ftqq.com/sendkey), 由于 Server酱已关闭 V1 key 的获取入口，所以实际上只有已持有 V1 send key 的老用户才能使用本渠道。
- **ftqq_v2_Key(使用 V2 版本 Server酱时必填)**: [Server酱 V2 send key](https://sct.ftqq.com/sendkey)

#### 上传代码
使用上传 ZIP 压缩包（推荐）的方式将包含构建后程序的函数目录完整上传至平台。

#### 添加定时触发器
在函数的「触发管理」中新建一个「定时触发器」，规则可根据需求定制。例如如果你希望探测函数每 5 分钟运行一次，则进行下图的配置：
![定时器示例](https://gmiimg.com/ccefcae3568cb512ab1c097b442d90f2.png)

#### 运行
当目标服务按预期正常响应时程序将在每次执行后返回成功 JSON 结果，否则程序将借由绑定的 Telegram Bot 发送如下格式的消息至目标聊天中，消息内容将包含拨测失败的错误内容：
```
Failed to test URL https://foo.foo due to error Get "https://foo.foo": dial tcp: lookup foo.foo on 10.10.10.10:53: read udp 10.10.10.10:5180->10.10.10.10:53: i/o timeout
```

## Todo
未来还考虑支持腾讯云之外的 Serverless 平台，包括但不限于：
- 阿里云 Serverless
- AWS Lambda
- Azure Serverless
- GCP Serverless

## 贡献代码
欢迎大家通过 PR 的形式来完善本工具或加入新的想法，PR 形式不限，提 PR 前做好 lint 即可。

## 费用说明
在实际使用中，是否会产生费用其实是与探测的「地址数量」和探测的「频次」是正相关的。

腾讯云 SCF 在公测结束后，对免费额度进行了调整，本工具比较容易触发的费用模式主要是「外网出流量计费」，可以参考[官方的文档](https://cloud.tencent.com/product/scf/pricing)。

由于外网出流量计出不计入，所以探测的逻辑中只有在发起 HTTP 请求时会被计量，接收响应则不会。因此在「地址数量」和「频次」均不高的时候，造成的费用非常低，在账单中会被校准为 0 元。但大规模使用的场景下则会累积较大量的外网出流量，造成事实上的费用，目前的费用标准是 0.8 元/GB 。

那么用量的「高」和「低」该如何判断呢？这里以我个人的使用经验来给大家举例作为参考：
- 函数量：1 个
- 监测地址数：5 个
- 监测频次：每 5 分钟请求一次
- 发送 TG 量：由于我手下有个站点不太稳定，通常一个月会产生 100+ 的发送量

腾讯云账单如下，即产生 0.06 元费用，校准后实际收费为 0 元：
![腾讯云账单](https://gmiimg.com/4865b1cbc976589f50f331bda007383a.png)
大家可以此为根据，假设流量和示例是线性关系来判断自己的用量是否会产生费用。

## 协议
[MIT](https://github.com/MrEasonYang/url-tester-func/blob/main/LICENSE)