---
title: 批量踢出离线Linux用户脚本
alias: 2016/06/17/remove-offline-user-shell
dir: /
date: 2016-06-17 16:50:08
tags: [Linux, Shell]
categories: [运维]
---


今天折腾VPS的时候发现有一台装着CentOS 7的VPS使用uptime命令时竟然提示已登录了90多个用户。w命令查看一下发现都是我一直使用的账户，登录IP也与本机的相符，前几个已登陆用户的空闲时间已经好几十天了，猜测可能是断开SSH的时候没有正常退出造成的。

Linux下踢掉用户使用 pkill -kill -t pts/*  即可，但是90多个待踢出的用户显然不应该手动操作，于是写了个小脚本，用遍历who命令第二列的结果结合上述命令kill掉所有用户，运行后重新登录SSH即可。

```sh
#! /bin/bash

for user_pts in $(who|awk '{print $2}')
do
    pkill -kill -t $user_pts
    echo $user_pts 'has been killed'
done
```