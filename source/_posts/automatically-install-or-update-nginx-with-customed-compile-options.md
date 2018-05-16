---
title: 以自定义参数自动编译安装或升级Nginx脚本
tags:
  - Linux
  - Shell
  - Nginx
  - 工具
categories: 运维
date: 2016-07-22 22:12:25
---


# 前言

之前偷懒直接在 VPS 上改了 repo 参数后用yum安装与更新 nginx，导致后期希望添加自选模块时遇到了困难。虽然可以采用 [为 yum 安装的 Nginx 添加模块](http://anyof.me/articles/236) 这里的方法为 yum 或者 apt 等方法成功安装的 Nginx 添加新的模块，但是在 yum 自动更新 nginx 后，又会回到二进制 nginx 安装包的默认编译参数，丢失之前新添加的模块选项。综上所述，目前只能通过再次编译安装的方式来升级 nginx 才能保证能正常使用自选模块。以下脚本将自动化该操作。

# 兼容性

在 CentOS 7.2 、Ubuntu 14.04 、Ubuntu 16.04 中测试正常。

# 配置

`git clone https://github.com/MrEasonYang/shell-boy` 后，cd 到 auto-nginx 目录中，执行`chmod +x auto-nginx`即可完成初始化。随后在配置文件 config 中按需自行配置。配置文件以英文分号 ; 分隔，每个选项和值之间用无空格英文冒号分隔 。

配置选项包括：<!--more-->

* configure-options：编译选项（模块为主）
* user：nginx 用户
* group：nginx 用户组
* prefix：配置文件位置
* sbin-path：可运行文件位置
* modules-path：模块目录位置
* conf-path：nginx.conf 的路径
* cc-opt：gcc编译参数
* install-as-service：是否在安装时同时安装为服务并启动
* original-sbin-path：升级前可执行文件路径

# 使用

* 安装指定版本 nginx ：`./auto-nginx install xx.x.xx`
* 自动更新已安装的 nginx ：`./auto-nginx update`
* 通过 `... 2>&1 | tee -a log` 的方法即可记录日志
* 向 crontab 添加例如 `0 23 * * 6 /auto-nginx/auto-nginx update 2>&1 | tee -a /auto-nginx/log` 来实现定期自动更新


P.S. 安装 Nginx 的功能中默认选择了一些依赖，如有需要自定义依赖，请自行更改脚本中的 apt-dependence 函数或 yum-dependence 函数。

# 实现

## 检查最新版本

检查版本需要一个小爬虫，虽然使用 Python 来实现最方便，但这里还是避免跑题，统一用 Shell 来做。

观察 Nginx 官网可以发现，下载地址总是 http://nginx.org/download/nginx-x.x.x.tar.gz 这样的形式，因此获取到最新的版本号，就可以直接下载 Nginx 的压缩包。而在 http://nginx.org/download/ 这个页面中乱序以 `<a href="nginx-0.1.0.tar.gz">nginx-0.1.0.tar.gz</a>` 的形式罗列着所有的版本，借助这个页面及链接就可以配合正则表达式来获取所有版本号。

```sh
    local version_data=$(curl -s http://nginx.org/download/ | awk -F '"' '/nginx/{print $2}' | grep 'tar.gz$'  | awk -F '-' '{print $2}' | awk -F '.' '{printf("%d.%d.%d\n", $1, $2, $3)}')
```

由于获取到的版本号是一个乱序排列的数组字符串数组，因此需要对该数组进行排序，筛选出最大也就是最新的版本号。

在排序的时候需要先使用 IFS 来分割 x.x.x 形式的版本号字符串，然后用一个循环和 expr 表达式将其转换为整数数组。

```sh
    local factor
    local array
    local count
    for item in $version_data
    do
        IFS=.
        array=($item)
        IFS=$' \t\n'
        count=0
        factor=1000000
        declare -a data
        for number in ${array[@]}
        do
            count=`expr $count + $(( 10#$number )) \* $factor`
            data+=($count)
            factor=`expr $factor / 1000`
        done
    done
```

再用 sort -nur 命令将数组排序，最后就能获取最大版本号，将其转回字符串，以进行下一步操作。

```sh
get_latest_version() {
    declare -a sorted_data
    sorted_data=($(for item in ${data[@]}; do echo $item; done | sort -nur))
    version_data=${sorted_data[0]}
    local right=`expr $version_data % 1000`
    local middle=`expr $version_data / 1000 % 1000`
    local left=`expr $version_data / 1000000`
    latest_version="$left.$middle.$right"
}
```

## 备份配置

由于本脚本主要用于更新 Nginx ，所以自然需要将原 Nginx 配置文件在更新前备份，在更新后恢复。

```sh
backup_nginx_config() {
    cp -r $conf_path/* backup/
}

recover_nginx_config() {
    rm -f $conf_path/*
    cp -r backup/* $conf_path
}
```

## 配置环境

随后进行安装或更新前的环境配置，主要就是安装各种依赖。

这里使用 apt-get 或 yum 来安装依赖（暂不支持 SUSE 的 zypper ），因此需要先判断当前是哪一个发行版。借助 /etc/*release 和 grep 命令，可以较为准确的区分当前发行版是 RHEL 系还是 Debian 系。注意由于 CentOS 7 使用了新的 systemctl 来代替 service，所以需要特殊对待。

```sh
check_distribution() {
    if cat /etc/*release|grep -q -i centos;then
        return 1
    elif cat /etc/*release|grep -q -i rhel;then
        return 1
    elif cat /etc/*release|grep -q -i ubuntu;then
        return 0
    elif cat /etc/*release|grep -q -i debian;then
        return 0
    fi
}
```

然后就可以根据发行版使用不同的包管理工具安装依赖了。

```sh
yum-dependence() {
    yum -y update
    yum -y install gc gcc gcc-c++ autoconf automake libtool make cmake libtool pcre-devel zlib zlib-devel openssl openssl-devel libxml2-devel libxslt-devel gd-devel perl-ExtUtils-Embed GeoIP-devel gperftools gperftools-devel libatomic_ops-devel perl-ExtUtils-Embed
}

apt-dependence() {
    apt-get -y update
    apt-get -y install gcc build-essential libc6 libpcre3 libpcre3-dev libssl-dev zlib1g zlib1g-dev lsb-base openssl libssl-dev libgeoip1 libgeoip-dev google-perftools libgoogle-perftools-dev libperl-dev libgd2-xpm-dev libatomic-ops-dev libxml2-dev libxslt1-dev python-dev
}
```

## 下载并编译安装 Nginx

```sh
download_and_install_nginx() {
    curl -s http://nginx.org/download/nginx-${1}.tar.gz -o download/nginx-${1}.tar.gz
    tar -zxf download/nginx-${1}.tar.gz -C data
    cd data/nginx-${1}
    groupadd nginx
    useradd -s /sbin/nologin -g nginx nginx
    echo $(pwd)
    ./configure --user=$user --group=$group --prefix=$prefix --sbin-path=$sbin_path --modules-path=$modules_path --conf-path=$conf_path $configure_parameters --with-cc-opt="$cc_opt"
    make
    if ${original_sbin_path}/nginx -v | grep version;then
        make upgrade
    else
        make install
    fi
    cd ../..
    rm download/nginx-${1}.tar.gz
    rm -rf data/nginx-${1}
}
```

## 设置服务与自启动

由于还提供了 Nginx 的安装功能，所以要加上设置服务与自启动的功能。

### Systemd

对于较新版本的的 Debian / Ubuntu 来说，直接使用心得 systemd 来管理服务即可，将以下内容：

```
[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

保存为 /lib/systemd/system/nginx.service ，然后：

```sh
            chmod +x /lib/systemd/system/nginx.service
            systemctl daemon-reload
            systemctl start nginx
            systemctl enable nginx
```

即可开启 nginx 并实现 nginx 的开机自启动功能。

### service

而对于使用 service 的发行版，则需要写一个较为复杂的 service 文件放在 /etc/init.d 下来实现通过 service 命令来完成各种操作：

```sh
#! /bin/sh

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
NGINX_PATH=/usr/sbin/nginx
PID_PATH=/var/run/nginx.pid
CONFIG_PATH=/etc/nginx/nginx.conf

start() {
    if netstat -ntlp | grep -q -i nginx;then
        echo "Nginx is running."
        exit 0
    fi
    
    $NGINX_PATH -c $CONFIG_PATH
    if [ "$?" != 0 ] ; then
        echo "Failed to start nginx."
        exit 0
    else
        echo "Nginx has been successfully started."
    fi
}

stop() {
    if ! netstat -ntlp | grep -q -i nginx; then
        echo "Nginx is not running."
        exit 0
    fi
    $NGINX_PATH -s stop
    if [ "$?" != 0 ] ; then
        kill `pidof nginx`
        if [ "$?" != 0 ] ; then
            echo "Failed to stop or kill nginx."
            exit 0
        else
            echo "Nginx process has been successfully killed."
        fi
    else
        echo "Nginx has been successfully stopped."
    fi
}

status() {
    if netstat -ntlp | grep -q -i nginx; then
        PID=`pidof nginx`
        echo "Nginx is running."
    else
        echo "Nginx is not running."
        exit 1
    fi
}

reload() {
    if netstat -ntlp | grep -q -i nginx; then
        $NGINX_PATH -s reload
        echo "Reloaded."
    else
        echo "Failed reload nginx."
        exit 0
    fi
}

test() {
    $NGINX_PATH -t
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    status)
        status
        ;;
    restart)
        stop
        start
        ;;
    reload)
        reload
        ;;
    test)
        test
        ;;

    *)
        echo "Invalid command"
        exit 0
esac
```

此时要实现开机自启，就是把 service nginx start 添加到 /etc/rc.local 中。

```sh
            cp service/service /etc/init.d/nginx
            chmod +x /etc/init.d/nginx
            echo 'service nginx start' >> /etc/rc.local
            service nginx start
```
本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。