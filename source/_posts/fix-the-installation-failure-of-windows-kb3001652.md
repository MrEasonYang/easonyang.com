---
title: 解决Windows安装Microsoft Visual Studio 2010 Tools for Office Runtime(kb3001652)提示0x80070643错误的问题
date: 2016-08-10 09:39:18
updated: 2016-08-10 09:39:18
tags: [Windows]
categories: [教程]
keywords: [Windows, kb3001652, 0x80070643, update error]
---

## 环境

Windows 10

Microsoft Office 2016

Visual Studio 2015

## 前言

自去年以来，KB3001652 也就是 Microsoft Visual Studio 2010 Tools for Office Runtime 这个功能补丁安装失败的提示就一直存在于我的系统更新日志中，之前忙东忙西无暇顾及，这次恰逢更新 Windows 10 到周年纪念版，实在不想再忍，决心彻底解决这个问题。

在 Windows 的事件管理器中看到错误信息的与更新日志中的一样，没有什么详细信息。尝试 Google 这个补丁编号和错误码，得到建议大多是用某种方法屏蔽掉这个补丁的推送，尝试未果，但是找到了 Microsoft Visual Studio 2010 Tools for Office Runtime 的官方下载地址，想到既然是功能性补丁，不如我手动安装下试试。<!--more-->

## 解决

在 https://www.microsoft.com/en-us/download/details.aspx?id=48217 下载该补丁后安装，安装到一半提示：

![error](https://gmiimg.com/2d16d0c6216f824184b7030102c9d688.png)

同时提示我可以到 `C:\2364710792f81b5c062a9f3a` 目录下去查找 vc_red.msi ，然而我并没有这个目录，这时合理猜测一下可能是之前安装某个类似工具的时候后我误删了该文件夹或该工具残留了部分安装信息，导致现在安装别的工具在该路径下找不到这个 vc_red.msi。

Google 到这个 vc_red.msi 是 [Microsoft Visual C++ 2010 Redistributable Package (x86)](https://download.microsoft.com/download/C/6/D/C6D0FD4E-9E53-4897-9B91-836EBA2AACD3/vcredist_x86.exe) 安装时用到的一个安装包，我的系统中已经装有这个功能了，但是还是先下载下来。尝试将该安装包解压后再进行 Microsoft Visual Studio 2010 Tools for Office Runtime 的安装，仍然提示找不到该 msi 。单独安装 Microsoft Visual C++ 2010 Redistributable Package (x86) 也仍旧报错。

尝试用神器 Everything 查找一下 vc_red.msi 东西还不少：

![everything](https://gmiimg.com/1332934a9503d9b4920719851bb1991f.png)

随意打开一个日志文件：

```
=== Verbose logging started: 12/3/2015  13:14:36  Build type: SHIP UNICODE 5.00.10011.00  Calling process: c:\7cca55705e629aa21867f85e67\Setup.exe ===
MSI (c) (10:E0) [13:14:36:961]: Resetting cached policy values
MSI (c) (10:E0) [13:14:36:961]: Machine policy value 'Debug' is 0
MSI (c) (10:E0) [13:14:36:961]: ******* RunEngine:
           ******* Product: c:\7cca55705e629aa21867f85e67\vc_red.msi
           ******* Action: 
           ******* CommandLine: **********
MSI (c) (10:E0) [13:14:36:962]: Client-side and UI is none or basic: Running entire install on the server.
MSI (c) (10:E0) [13:14:36:962]: Grabbed execution mutex.
MSI (c) (10:E0) [13:14:37:015]: Cloaking enabled.
MSI (c) (10:E0) [13:14:37:015]: Attempting to enable all disabled privileges before calling Install on Server
MSI (c) (10:E0) [13:14:37:018]: Incrementing counter to disable shutdown. Counter after increment: 0
MSI (s) (0C:50) [13:14:37:031]: Running installation inside multi-package transaction c:\7cca55705e629aa21867f85e67\vc_red.msi
MSI (s) (0C:50) [13:14:37:031]: Grabbed execution mutex.
MSI (s) (0C:50) [13:14:37:034]: Resetting cached policy values
MSI (s) (0C:50) [13:14:37:034]: Machine policy value 'Debug' is 0
MSI (s) (0C:50) [13:14:37:034]: ******* RunEngine:
           ******* Product: c:\7cca55705e629aa21867f85e67\vc_red.msi
           ******* Action: 
           ******* CommandLine: **********
MSI (s) (0C:50) [13:14:37:035]: Machine policy value 'DisableUserInstalls' is 0
MSI (s) (0C:50) [13:14:37:045]: User policy value 'SearchOrder' is 'nmu'
MSI (s) (0C:50) [13:14:37:045]: User policy value 'DisableMedia' is 0
MSI (s) (0C:50) [13:14:37:045]: Machine policy value 'AllowLockdownMedia' is 1
MSI (s) (0C:50) [13:14:37:045]: SOURCEMGMT: Looking for sourcelist for product {F0C3E5D1-1ADE-321E-8167-68EF0DE699A5}
MSI (s) (0C:50) [13:14:37:045]: SOURCEMGMT: Adding {F0C3E5D1-1ADE-321E-8167-68EF0DE699A5}; to potential sourcelist list (pcode;disk;relpath).
MSI (s) (0C:50) [13:14:37:045]: SOURCEMGMT: Now checking product {F0C3E5D1-1ADE-321E-8167-68EF0DE699A5}
MSI (s) (0C:50) [13:14:37:045]: SOURCEMGMT: Attempting to use LastUsedSource from source list.
MSI (s) (0C:50) [13:14:37:045]: SOURCEMGMT: Trying source C:\2364710792f81b5c062a9f3a\.
MSI (s) (0C:50) [13:14:37:046]: Note: 1: 2203 2: C:\2364710792f81b5c062a9f3a\vc_red.msi 3: -2147287037 
MSI (s) (0C:50) [13:14:37:046]: SOURCEMGMT: Source is invalid due to missing/inaccessible package.
MSI (s) (0C:50) [13:14:37:046]: Note: 1: 1706 2: -2147483647 3: vc_red.msi 
MSI (s) (0C:50) [13:14:37:046]: SOURCEMGMT: Processing net source list.
MSI (s) (0C:50) [13:14:37:046]: Note: 1: 1706 2: -2147483647 3: vc_red.msi 
MSI (s) (0C:50) [13:14:37:046]: SOURCEMGMT: Processing media source list.
MSI (s) (0C:50) [13:14:37:046]: Note: 1: 2203 2:  3: -2147287037 
MSI (s) (0C:50) [13:14:37:047]: SOURCEMGMT: Source is invalid due to missing/inaccessible package.
MSI (s) (0C:50) [13:14:37:047]: Note: 1: 1706 2: -2147483647 3: vc_red.msi 
MSI (s) (0C:50) [13:14:37:047]: SOURCEMGMT: Processing URL source list.
MSI (s) (0C:50) [13:14:37:047]: Note: 1: 1402 2: UNKNOWN\URL 3: 2 
MSI (s) (0C:50) [13:14:37:047]: Note: 1: 1706 2: -2147483647 3: vc_red.msi 
MSI (s) (0C:50) [13:14:37:047]: Note: 1: 1706 2:  3: vc_red.msi 
MSI (s) (0C:50) [13:14:37:047]: SOURCEMGMT: Failed to resolve source
MSI (s) (0C:50) [13:14:37:048]: MainEngineThread is returning 1612
MSI (s) (0C:50) [13:14:37:051]: User policy value 'DisableRollback' is 0
MSI (s) (0C:50) [13:14:37:051]: Machine policy value 'DisableRollback' is 0
MSI (s) (0C:50) [13:14:37:051]: Incrementing counter to disable shutdown. Counter after increment: 0
MSI (s) (0C:50) [13:14:37:051]: Note: 1: 1402 2: HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Installer\Rollback\Scripts 3: 2 
MSI (s) (0C:50) [13:14:37:051]: Note: 1: 1402 2: HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Installer\Rollback\Scripts 3: 2 
MSI (s) (0C:50) [13:14:37:052]: Decrementing counter to disable shutdown. If counter >= 0, shutdown will be denied.  Counter after decrement: -1
MSI (c) (10:E0) [13:14:37:054]: Decrementing counter to disable shutdown. If counter >= 0, shutdown will be denied.  Counter after decrement: -1
MSI (c) (10:E0) [13:14:37:054]: MainEngineThread is returning 1612
=== Verbose logging stopped: 12/3/2015  13:14:37 ===
```

果然与之前的猜测一致，`LastUsedSource` 的值为 `C:\2364710792f81b5c062a9f3a\` ，造成补丁安装程序安装时希望在这个不存在的位置找到 vc_red.msi ，导致安装失败。而日志开头的：

```
MSI (c) (10:E0) [13:14:36:961]: ******* RunEngine:
           ******* Product: c:\7cca55705e629aa21867f85e67\vc_red.msi
           ******* Action: 
           ******* CommandLine: **********
```

意味着本次安装前，安装程序就已经将需要的内容解压到了 `c:\7cca55705e629aa21867f85e67\`  下。

由此再进行一次安装，在提示找不到 vc_red.msi 时将路径指向安装程序最新创建的目录下，通常位于某一盘的根目录下，终于安装成功 Microsoft Visual C++ 2010 Redistributable Package (x86)，随后成功安装 Microsoft Visual Studio 2010 Tools for Office Runtime 。

在 Windows 更新中也不再提示该补丁的安装，问题得以解决。

![result](https://gmiimg.com/8737ab9bdaeb857db69bf7ea8b8ab5e0.png)

本文由 [Eason Yang](https://easonyang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://easonyang.com/about/)。