---
title: flomo-cli中文说明
date: 2021-07-17 13:45:43
updated: 2021-07-17 13:45:43
alias: flomo-cli-cn-readme
tags: [工具, 开源]
categories: [资源与工具]
keywords: [flomo, flomo-cli, GitHub, 开源]
---
# 什么是 flomo-cli
这是一款可以在命令行中将笔记和想法保存到 [flomo](https://flomoapp.com/) 的工具。
基于 Golang 实现，可通过 Homebrew 便捷安装。
Github Repo：[https://github.com/MrEasonYang/flomo-cli](https://github.com/MrEasonYang/flomo-cli)<!--more-->

# 安装
## 从源码编译安装
保证环境中已安装 1.16 版本以上的 Golang ，执行以下命令即可：
```shell
git clone git@github.com:MrEasonYang/flomo-cli.git
cd flomo-cli
go build
```
## 使用Homebrew安装
在 Homebrew 中输入以下命令即可完成安装。
```shell
brew tap MrEasonYang/taps
brew install flomo
```
目前支持以下平台：
- Apple Intel AMD64
- Apple Silicon
- Linux AMD64
## 手动下载安装
如果不喜欢 Homebrew 或正在使用 Windows 系统，那么你可以访问 [Release](https://github.com/MrEasonYang/flomo-cli/releases) 下载对应平台的最新版本并手动进行配置。

# 使用
## 配置
访问 [Flomo 个人配置页面](https://flomoapp.com/mine?source=incoming_webhook) 以获取个人的开放 API ，随后执行以下命令配置 API 到 flomo-cli 中:
```shell
flomo set api ${Flomo API}
```
随后 flomo-cli 将会在用户目录生成名为 `.flomo-cli.config` 的隐藏文件，该文件的权限为 0600 。
## 保存Memo
Memo 即 flomo 概念下的笔记，只需在各类终端工具的命令行中输入以下命令即可
```shell
flomo save ${Your memo content}
```
## 设置 alias
为了防止只使用 `flomo` 单个命令带来的误输入风险，目前 memo 的保存操作必须结合 `save` 关键字来进行。但如果你想简化输入，可以在 zsh/bash 等的配置文件中加入 alias 配置即可，示例如下：
```shell
alias flomo="flomo save" 
```

# 共享代码
由于 flomo 方面目前开放的 API 较少，本工具整体的功能还很简单。欢迎大家通过 PR 的形式来完善本工具或加入新的想法，PR 形式不限，提前做好 lint 即可。

# 协议
[MIT](https://github.com/MrEasonYang/flomo-cli/blob/main/LICENSE)