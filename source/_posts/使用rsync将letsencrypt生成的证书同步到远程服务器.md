---
title: 使用rsync将letsencrypt生成的证书同步到远程服务器
alias: 2016/09/09/use-rsync-to-sync-letsencrypt-certifications-with-remote-server
dir: /
date: 2016-09-09 09:11:46
updated: 2016-09-09 09:11:46
tags: [Linux, Git]
categories: [运维]
keywords: [rsync, letsencrypt, linux, ssh-agent, ssh-add, keychain, expect, 同步远程服务器]
---

最近在给博客搞一个容灾服务器，由于使用 Hexo ，所以网站数据数据的同步只需要在 _config.yml 中添加容灾服务器的部署配置信息即可，但是由于本站使用了 letsencrypt 实现全站 HTTPS ，需要实现 letsencrypt 在主服务器上自动循环注册之后自动地同步到容灾服务器，以避免容灾服务器的证书在3个月后过期的问题。这里使用 Linux 下经典的 rsync 来实现。

## 安装 rsync

rsync 不比 nginx 等程序，稳定可用即可，因此直接从包仓库安装：

```sh
yum install rsync #CentOS、RHEL、Fedora
apt-get install rsync #Debian、Ubuntu
```

## 选择同步方式

rsync 支持两种同步方式，一种是使用自带的守护进程运行一个 rsync daemon 服务器，然后对方使用 socks5 来连接服务器传输文件，另一种是直接使用 SSH 进行认证和数据传输。

前者的用户名密码都是以 `username:password` 这样的形式明文存在 secrets 文件中的，传输过程默认也没有使用加密方式，很方便进行中间人攻击，同时还要运行一个守护程序吃服务器性能，所以不太适合本文要进行远程传输的需求。故本文选择使用 SSH 方式进行同步。<!--more-->

## 配置账户及 SSH

为了安全起见，我们在目标服务器中新建一个用户，`adduser receiver` ，用 passwd 设置好密码后参照 [为Git设置SSH公钥及私钥](https://easonyang.com/2016/07/31/set-ssh-identity-file-for-git/) 配置好公钥和私钥。

配置公钥私钥的原因在于我们希望避免同步的时候提示输入账户密码，然而如果在使用 `ssh-keygen` 生成密钥时添加了密码（passphrase），那么我们在每一次使用密钥进行同步的时候仍然会被要求手动输入密码，下面提供几种解决方案：

### 1.使用无密码密钥（不推荐）

这个最好处理，只要在生成密钥的时候回车跳过设置密码即可。但是安全问题也是显而易见的，一旦私钥泄露，那么目标服务器的数据就岌岌可危了。如果使用这种方法，一定要设置好私钥的可访问用户和权限，尽管这只能理论上保证其安全。

### 2.使用 ssh-agent

常用 Git 的人对 ssh-agent 肯定不会陌生，借助这个工具，我们只要在终端运行时执行 `ssh-add <path/to/rsa>` （不加具体路径则会扫描添加 `~/.ssh/id_rsa`），即可使密码保存到内存中（安全的），在本终端不关闭的情况下都无需输入密钥的密码了。缺点是受其生命周期限制，重启终端、重启系统都会造成要求重新输入密码的结果。可以使用如下脚本来判断 ssh-agent 是否在运行，如果未运行则启动 ssh-agent ：

```sh
if ! [ -n "$SSH_AUTH_SOCK" ] || 
    ! { ssh-add -l &>/dev/null; rc=$?; [ "$rc" -eq 0 ] || [ "$rc" -eq 1 ];}; then
    eval "$(ssh-agent -s)"
fi
```

### 3.使用 keychain 等工具配合 ssh-agent

由于 ssh-agent 的局限性，网络上衍生出了一些配套工具来延长 ssh-agent 的生命周期到上次重启到下次重启之间，也就是只要不重启系统，就无需再输入密码。这类工具有 [keychain](http://www.funtoo.org/Keychain) 和 [envoy](https://github.com/vodik/envoy) 等，具体的安装使用参照官网，其中 keychain 在一些老旧的 Linux 发行版中是可以直接用包管理工具安装的。这些工具也有一个缺点，那就是一旦系统重启就必须要重新输入密码，但这一点对于有服务器重启监控并且 uptime 超过30天的服务器来说是可以接受的。

### 4.使用 expect 向 ssh-agent 发送密码（不推荐）

这种方法实际上是通过脚本运行 ssh-add 命令，然后将脚本中存储的密码传给 ssh-add ，但是由于 ssh-agent 没有使用标准输入，所以我们不能像写 shell 脚本那样直接向 ssh-add 传递参数，这就需要借助 expect 来向 ssh-add 发送数据。使用 yum 或 apt 等安装好 expect ，创建以下脚本并给予其可运行权限：

```sh
#!/usr/bin/expect -f
spawn ssh-add /home/sender/.ssh/rsync_rsa
expect "Enter passphrase for /root/.ssh/rsync_rsa:"
send "put password here\n";
interact
```

随后在 crontab 或着 letsencrypt 脚本的底部运行上述脚本，就可以 ssd-add 的操作自动化。缺点也是极大的，使用密钥却明文存储密钥密码这种事无异于掩耳盗铃，所以采用这种方法同样要设置好该脚本的可访问用户和权限，来保证其他人员理论上无法查看该脚本。这个方法来自于：https://unix.stackexchange.com/questions/90853/how-can-i-run-ssh-add-automatically-without-password-prompt 这个问题中 [Thomas Nyman](https://unix.stackexchange.com/users/43779/thomas-nyman) 的回答。

### 5.使用 ssh-pass 直接传递密码（极不推荐）

顾名思义，及通过 ssh-pass 命令，在使用 rsync 的同时，传递密码，具体是否可行并未尝试，因为将明文密码暴露于终端的历史纪录中的危险性要远大于上述几种方法，因此极不推荐。

## 使用 rsync 进行同步

配置完成了 SSH 后就可以使用 rsync 命令进行同步了：

```sh
rsync -avz -e "ssh -p 2333 -i /home/sender/.ssh/rsync_account_rsa" /from/* rsync@192.168.101.101:/to/
```

注意 `/from/*` 指本地目录，`/to/` 指目标目录，顺序不能颠倒。 -e 后面接的是使用 ssh 登录，-p 是目标服务器的 SSH 端口（默认22），-i 是私钥的路径（默认为 `~/.ssh/id_rsa`）。

将上述命令添加到 letsencrypt 脚本的最后，即可在完成证书重新生成后自动同步新证书到远程服务器的要求了。

## 后记

其实还可以将证书目录做为一个 Git 仓库，这样容灾服务器只要每次 pull 一下就可以了，但是这样做就要添加任务计划才能实现自动化，稍微有些不方便。具体的使用方法只有第一步和最后一步不同，配置 SSH 的方法都是相同的，这里不再赘述。