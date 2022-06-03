---
title: 为Git设置SSH公钥及私钥
alias: 2016/07/31/set-ssh-identity-file-for-git
dir: /
date: 2016-07-31 15:45:33
tags:
  - Windows
  - Linux
  - Git
categories: [运维]
---

在使用 Git 进行代码管理时，如果我们不使用 SSH 公钥及密钥，那么我们就必须在每次执行 Git 命令后键入密码来验明正身，所以为 Git 配置 SSH 公钥及密钥是很有必要的。下文对 Windows 及 Linux 系统，自建 Git 及 Github 均适用。

## 配置公钥

首先通过 `ssh-keygen -t rsa -b 4096 -C "name@example.com"` 或者 XShell 的相应功能来生成一对密钥和公钥。随后我们得到了一个 id_rsa 和 id_rsa.pub （名称来源于你的定义，本文统一视作 id_rsa），其中 id_rsa 为私钥，id_rsa.pub 为公钥，我们需要把这个公钥的内容上传到服务器对应 Git 用户的  `.ssh/authorized_keys` 目录中，并在 SSH 的配置文件中允许通过公钥登陆。对于 Github 来说则是上传到 Github 中的公钥设置页面中。如果遇到提示未在远程服务器注册的问题，请执行 `chmod 600 authorized_keys` 。至此公钥配置完毕。<!--more-->

## 配置私钥

私钥则需要在客户端也就是我们自己的计算机上进行配置。无论你使用 Windows 还是 Linux，私钥都应该放置于用户账户目录下的 `.ssh` 中，对于 Windows 则是 `C:\Users\用户名\.ssh` ，对于 Linux 则是 `/home/用户名/.ssh` 中。

随后我们在 .ssh 目录下新建一个 config 文件，将以下内容写入该文件：

```
Host 远程服务器域名或IP
User 登陆用的Git用户名
PreferredAuthentications publickey
IdentityFile /c/Users/用户名/.ssh/id_rsa
```

这样一来就可以在客户端直接使用 Git 而无须输入密码了。

## 多个 Git 服务器的密钥配置

我们经常会同时使用多个不同的 Git 服务器，例如同时使用 Github 和 自建 Git 服务。虽然我们可以使用同一对私钥和公钥来连接所有 Git 服务器，但是这有较大的安全风险。因此我们需要为每台 Git 服务器生成不同的公钥及私钥，然后在每个 Git 服务器上按上文配置公钥，在客户端中通过向 config 文件添加多个 Host 来解决这一问题。实例如下：

```
Host 192.168.1.10
User git
PreferredAuthentications publickey
IdentityFile /c/Users/用户名/.ssh/id_rsa_a

Host 192.168.1.11
User git
PreferredAuthentications publickey
IdentityFile /c/Users/用户名/.ssh/id_rsa_b
```

## Windows 下配置 Git 私钥的注意事项

* 由于 Windows 的目录也就是文件夹名称是允许空格存在的，而 Windows 下的 Git 客户端都是按照 Linux 的无空格标准来执行的，所以你的私钥如果保存到了带空格的文件夹下，就无法正常使用私钥登陆。此时只要把私钥移动至路径无空格的目录下，然后修改 config 文件中的 IdentityFile 为新路径即可。
* IdentityFile 的值需要填写 Linux 下的路径格式，如 `/c/Users/eason/.ssh` 而不能是 windows 下的 `C:\Users\eason\.ssh` 。