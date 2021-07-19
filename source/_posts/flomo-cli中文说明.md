---
title: flomo-cli中文说明
date: 2021-07-17 13:45:43
updated: 2021-07-17 13:45:43
alias: flomo-cli-cn-readme
tags: [工具, 开源]
categories: [资源与工具]
keywords: [flomo, flomo-cli, GitHub, 开源]
---
## 什么是 flomo-cli
这是一款可以在命令行中将笔记和想法保存到 [flomo](https://flomoapp.com/) 的工具。
基于 Golang 实现，可通过 Homebrew 便捷安装。
GitHub Repo：[https://github.com/MrEasonYang/flomo-cli](https://github.com/MrEasonYang/flomo-cli)

## 功能
- 一行命令即可创建 flomo 笔记。
- 支持编辑器模式，可使用 vim/neovim/emacs 创建笔记。
- 支持 shell 管道，快速保存文件或过滤结果。<!--more-->

## 安装
### 从源码编译安装
保证环境中已安装 1.16 版本以上的 Golang ，执行以下命令即可：
```shell
git clone git@github.com:MrEasonYang/flomo-cli.git
cd flomo-cli
go build
```
### 使用Homebrew安装
在 Homebrew 中输入以下命令即可完成安装。
```shell
brew tap MrEasonYang/taps
brew install flomo
```
目前支持以下平台：
- Apple Intel AMD64
- Apple Silicon
- Linux AMD64
### 手动下载安装
如果不喜欢 Homebrew 或正在使用 Windows 系统，那么你可以访问 [Release](https://github.com/MrEasonYang/flomo-cli/releases) 下载对应平台的最新版本并手动进行配置。

## 使用
### 配置
访问 [Flomo 个人配置页面](https://flomoapp.com/mine?source=incoming_webhook) 以获取个人的开放 API ，执行以下命令配置 API 到 flomo-cli 中:
```shell
flomo set api ${Flomo API}
```
随后 flomo-cli 将会在用户目录生成名为 `.flomo-cli.config` 的隐藏文件，该文件的权限为 0600 。
### 一键保存
Memo 即 flomo 概念下的笔记，只需在各类终端工具的命令行中输入以下命令即可
```shell
flomo save ${Your memo content}
```
### Shell 管道
Flomo-cli 如常见程序一样，支持以管道的数据重定向内容作为笔记内容，可借助 `cat` 等命令快速保存文件等内容：
```shell
cat memo.txt | flomo
```
### 编辑器模式
除了直接在命令行中输入，flomo-cli 也支持使用编辑器进行笔记编写和保存，只需要执行以下命令即可：
```shell
# Open vim to compose the memo.
flomo vim 

# Open neovim to compose the memo.
flomo nvim 

# Open emacs to compose the memo.
flomo emacs
```
目前 flomo-cli 只对 `vim/neovim/emacs` 进行了支持, 输入其他内容将抛出异常以避免任意执行带来的安全问题。
### 清理临时文件
编辑器模式的实现思路是在接收到命令时调用指定编辑器对 `~/.flomo-tmp` 目录的临时文件进行编辑并一直等待。当用户退出编辑器时停止等待，接着将临时文件的内容作为笔记发送至 flomo ，最后将临时文件删除。
这样一来，如果存在并发调用或强制终止 flomo-cli 的情况，则临时文件的删除工作可能就会被中断，进而造成堆积的临时文件占用磁盘空间。对于这一问题可以执行以下命令一键清理临时文件：
```shell
flomo clear
```

## 设置 alias
为了防止只使用 `flomo` 单个命令带来的误输入风险，目前笔记的保存操作必须结合 `save` 关键字来进行。如果你希望简化输入，那么只需要在 zsh/bash 等 shell 的配置文件中新增 alias 即可，示例如下：
```shell
alias flomo="flomo save" 
```

## 贡献代码
欢迎大家通过 PR 的形式来完善本工具或加入新的想法，PR 形式不限，提 PR 前做好 lint 即可。

## 协议
[MIT](https://github.com/MrEasonYang/flomo-cli/blob/main/LICENSE)