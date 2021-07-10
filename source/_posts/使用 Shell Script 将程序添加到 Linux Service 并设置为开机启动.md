---
title: 使用 Shell Script 将程序添加到 Linux Service 并设置为开机启动
alias: set-a-program-as-service-and-put-it-on-startup-in-linux
date: 2016-07-23 13:17:37
tags: [Linux, Shell]
categories: 运维
---

## 创建一个简单的测试脚本

```sh
#! /bin/bash

date|tee -a /program_log

case "$1" in
    start)
        echo "start" >> /program_log
        ;;
    stop)
        echo "stop" >> /program_log
        ;;
    *)
        echo "Wrong command" >> /program_log
esac
```

保存到 /program 然后 `chmod +x /program` ，并在根目录创建 program_log。这个程序会将当前时间追加到 /program_log 中，同时输出执行程序时输入的命令参数。执行 `/program start` 后可以看到 /program_log 中出现了时间和命令参数。<!--more-->

```
Mon Jul 25 14:25:58 UTC 2016
start
```

## 为使用 service 的发行版设置系统服务

在使用 service 的发行版（如 Ubuntu 14.04，Debian 7，CentOS 6 等）中，执行 `service xxx aaa` 这样的命令，实际上是 service 调用 `/etc/init.d/xxx` ，并将第二个参数 aaa 传入该 xxx 中。例如执行 `service nginx start` ，service 就会调用 `/etc/init.d/nginx` ，并将 start 传入 nginx 。因此我们需要为 program 程序编写一个 service 可执行文件保存到 `/etc/init.d/program-service` 。

```sh
#! /bin/bash

start() {
    /program start
}

stop() {
    /program stop
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    *)
        echo "Invalid option"
        ;;
esac
```

这个 service 实际上是调用位于根目录下的 program 脚本来执行各种命令。

此时如果执行 `service program-service start` 会提示 `program-service: unrecognized service` ，这是因为我们还没有更改 program-service 的权限为可执行。`chmod +x /etc/init.d/program-service` 后重新执行上一个命令，可以看到 program-log 变为：

```
Mon Jul 25 14:25:58 UTC 2016
start
Mon Jul 25 14:38:33 UTC 2016
start
```

至此该程序已经添加到了系统 service 命令中。

## 为使用 service 的发行版设置开机启动

对于大多数的 Linux 发行版而言，开机启动的配置信息保存在 `/etc/rc.local` ，所以我们只需要将 `service program-service start` 添加到 `/etc/rc.local` 中即可实现开机启动 program 程序。

## 为使用 systemd 的发行版设置系统服务

对于一些复杂的程序，service 的脚本可能也极其复杂，而在一些使用 systemd 的发行版（如 CentOS 7等）中则不存在这个问题。我们只需要编写一个 program-service.service 文件保存到 `/lib/systemd/system` 或 `/etc/systemd/system/` 中即可。

```
[Unit]
Description=Just a test
Documentation=https://easonyang.com

[Service]
Type=simple
ExecStart=/program start
ExecStop=/program stop
```

其具体的参数含义及配置方法可参照：

* https://wiki.archlinux.org/index.php/systemd
* https://www.freedesktop.org/software/systemd/man/systemd.service.html

另外在修改了 service 文件的一些选项后会提示需要重载 service ，这时候执行 `systemctl daemon-reload` 进行重载。

随后执行 `systemctl start program-service` 即可启动服务。

## 为使用 systemd 的发行版设置开机启动

经过上面的配置后，我们只需要执行 `systemctl enable program-service` 这一条命令即可实现开机启动。