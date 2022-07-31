---
title: 判断 Linux 发行版版本
url: 2016/07/23/get-linux-distribution-in-shell-script
date: 2016-07-23 13:03:19
tags: [Linux, Shell]
categories: [operations]
---

关于这个问题，有许多解决方案，首先可以使用

```sh
cat /etc/issue
```

结果类似于这样：

```
CentOS release 6.6 (Final)
Kernel \r on an \m
```

然而我在 CentOS 7 中使用时只显示<!--more-->

```
\S
Kernel \r on an \m
```

不利于脚本中的判断，通用性很堪忧，所以这个方法很有局限性。

另一种方法是：

```sh
cat /proc/version
```

这个方法的执行结果如下：

```
Linux version 3.13.0-36-generic (buildd@allspice) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1) ) #63-Ubuntu SMP Wed Sep 3 21:30:45 UTC 2014
Linux version 2.6.32-39-pve (root@lola) (gcc version 4.7.2 (Debian 4.7.2-5) ) #1 SMP Wed Jun 24 06:39:42 CEST 2015
Linux version 2.6.32-431.1.2.0.1.el6.i686 (mockbuild@c6b9.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Dec 13 11:45:23 UTC 2013
Linux version 3.10.0-229.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.2 20140120 (Red Hat 4.8.2-16) (GCC) ) #1 SMP Fri Mar 6 11:36:42 UTC 2015
```

上面这四行分别对应的是在 Ubuntu 14.04 ，Debian 7 ，CentOS 6 ，CentOS 7 中执行该命令的结果。可见该方法可以很好地区分 debian 系和 rhel 系的发行版，甚至也能很好地区分 Ubuntu 和 Debian。然而缺点也是显而易见的，那就是无法准确获取该发行版的版本，也就是无法知道这个 Ubuntu 是16.04 还是 14.04，CentOS 是 7 还是 6，要想获取准确版本号，仍需要一些辅助的判断。

而目前我使用较多的是下面这个方法：

```sh
cat /etc/*-release
```

或

```sh
cat /etc/*release
```

执行结果如下

```
PRETTY_NAME="Debian GNU/Linux 7 (wheezy)"
NAME="Debian GNU/Linux"
VERSION_ID="7"
VERSION="7 (wheezy)"
ID=debian
ANSI_COLOR="1;31"
HOME_URL="http://www.debian.org/"
SUPPORT_URL="http://www.debian.org/support/"
BUG_REPORT_URL="http://bugs.debian.org/"
easonyang@CT1157:~$ cat /etc/*release
PRETTY_NAME="Debian GNU/Linux 7 (wheezy)"
NAME="Debian GNU/Linux"
VERSION_ID="7"
VERSION="7 (wheezy)"
ID=debian
ANSI_COLOR="1;31"
HOME_URL="http://www.debian.org/"
SUPPORT_URL="http://www.debian.org/support/"
BUG_REPORT_URL="http://bugs.debian.org/"
```

```
CentOS Linux release 7.2.1511 (Core) 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

CentOS Linux release 7.2.1511 (Core) 
CentOS Linux release 7.2.1511 (Core
```

```
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=14.04
DISTRIB_CODENAME=trusty
DISTRIB_DESCRIPTION="Ubuntu 14.04.4 LTS"
NAME="Ubuntu"
VERSION="14.04.4 LTS, Trusty Tahr"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 14.04.4 LTS"
VERSION_ID="14.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/
```

可见获取的信息非常丰富，在 CentOS 6/7 ，Ubuntu 14.04/16.04 ，Debian 7 中测试可用，所以理论上这个方法是最方便实用的。

然而网络上有反应道有的发行版中并没有 `/etc/*-release` 这种文件，鉴于手上只有这几种系统可以测试，所以无法验证这个说法是否可信，因此此方法可能缺乏通用性。

此外，如果你已经通过上文方法确定了本系统是 CentOS，那么你可以使用 `rpm -q centos-release` 来更方便地获取 CentOS 的具体版本号。例如 `rpm -q centos-release | grep '\-7\-'`

## 总结

这个问题似乎没有完美的解决方案，最保险的做法就是综合这几种方法，多做几层判断，多考虑几种情况。