---
title: 自动安装与升级chacha20(libsodium)
date: 2016-09-18 08:55:26
updated: 2016-09-18 08:55:26
tags:
  - Linux
  - Shell
  - 工具
categories: 运维
keywords: [chacha20, libsodium, linux, shell, 自动脚本, 运维]
---

## 前言

由于我经常要用到 chacha20 这种加密方式，而目前稳定版本的 openssl 等都不自带这个包，所以每次配置新的服务器都要手动装一遍，实在是浪费人生。索性直接写个小脚本来自动安装与升级。

## 使用

`git clone https://github.com/MrEasonYang/shell-boy` 后，cd 到 shell/auto-chacha20 目录中，`chmod +x auto-chacha20.sh` ，然后 `./auto-chacha20.sh` 运行即可。由于默认配置下，不用做 Nginx 安装脚本中那样备份等操作，所以更新时实际上仍然是运行 `./auto-chacha20.sh` ，加入 crontab 中就可以实现定期自动更新了，如加入 `0 2 * * 3 /home/user/auto-chacha20 2>&1 | tee -a /home/user/auto-chacha20/log` ，即可实现每周三的凌晨两点检查更新。

## 兼容性

在 CentOS 7.2 、Ubuntu 14.04 、Ubuntu 16.04 中测试正常。

## 获取最新版本版本号

chacha20 的安装实际上就是编译安装 libsodium ，所以我们要设法获取到最新版本的版本号。在官网的 [下载页面](https://download.libsodium.org/libsodium/releases/) 观察一下就可以发现，这个列表与我之前在 [以自定义参数自动编译安装或升级Nginx脚本](https://easonyang.com/2016/07/22/automatically-install-or-update-nginx-with-customed-compile-options/) 中分析 Nginx 官网下载页面的形式是完全一样所以直接借鉴 Nginx自动安装脚本代码来获取最新版本的版本号。<!--more-->

```sh
check() {
    local version_data=$(curl -s https://download.libsodium.org/libsodium/releases/ | awk -F '"' '/libsodium/{print $2}' | grep 'tar.gz$'  | awk -F '-' '{print $2}' | awk -F '.' '{printf("%d.%d.%d\n", $1, $2, $3)}')
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
    get_latest_version $data
}

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

与 Nginx安装脚本一样，check 函数用于检测最新版本，在 curl 了官方下载页面并过滤出所有版本号后，调用 get_latest_version 函数进行排序与格式化，最终将版本号存在 latest_version 变量中。

## 安装依赖

本脚本主要针对比较主流的两种包管理，也就是 yum 和 apt 设置了自动安装依赖。原理与 [以自定义参数自动编译安装或升级Nginx脚本](https://easonyang.com/2016/07/22/automatically-install-or-update-nginx-with-customed-compile-options/) 和 [判断 Linux 发行版](https://easonyang.com/2016/07/23/get-linux-distribution-in-shell-script/) 中的解释是相同的。

```sh
yum-dependence() {
    yum -y update
    yum -y install m2crypto gcc make
}

apt-dependence() {
    apt-get -y update
    apt-get -y install gcc build-essential python-m2crypto
}

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

init_environment() {
    if [ 1 -eq $distribution ];then
        yum-dependence
    elif [ 0 -eq $distribution ];then
        apt-dependence
    fi
}
```

## 下载与安装

这一步封装在 install 函数中，实际上就是 curl 下载后解压，然后进行 configure 、make && make install ，但要注意最后还要将程序的安装位置和配置文件位置等信息写入配置文件 ld.so.conf 中，最后执行 ldconfig 加载配置文件才可以正常开启 chacha20 。

此外，为了实现自动更新时不会重复安装已安装版本，所以要有一个配置文件，存储当前版本号，未安装时版本号则为 0.0.0 ：

```
current-version:1.0.11;
```

随后要在脚本中获取配置信息：

```sh
get_shell_config() {
    IFS=';'$'\n'
    local config=($(cat auto-chacha20-config))
    local line
    for item in ${config[@]}
    do
        IFS=:
        line=($item)
        case ${line[0]} in
            current-version)
                current_version=${line[1]}
                ;;
        esac
    done
    IFS=$' \t\n'
}
```

最后，在脚本执行时，我们要判断当前已安装版本号与最新版本号是否相同，不相同时再执行安装，安装结束后还要借助 sed 命令，执行 `sed -i 's/current-version:.*;/current-version:'$latest_version';/g' ./auto-chacha20-config` 将最新的版本号写入配置文件中，同时覆盖原来的值：

```sh
install() {
    curl -s https://download.libsodium.org/libsodium/releases/libsodium-$latest_version.tar.gz -o libsodium.tar.gz
    tar -zvxf libsodium.tar.gz
    cd libsodium-$latest_version
    ./configure
    make && make install
    echo "include ld.so.conf.d/*.conf" > /etc/ld.so.conf
    echo "/lib" >> /etc/ld.so.conf
    echo "/usr/lib64" >> /etc/ld.so.conf
    echo "/usr/local/lib" >> /etc/ld.so.conf
    ldconfig
    cd ..
    rm libsodium.tar.gz
    rm -rf libsodium-$latest_version
    sed -i 's/current-version:.*;/current-version:'$latest_version';/g' ./auto-chacha20-config
}

get_shell_config
check
if [ "$latest_version" != "$current_version" ];then
    check_distribution
    distribution=$?
    init_environment
    install
fi
```



## 总结

最后完整脚本如下：

```sh
#! /bin/bash

latest_version=
distribution=
current_version=

get_shell_config() {
    IFS=';'$'\n'
    local config=($(cat auto-chacha20-config))
    local line
    for item in ${config[@]}
    do
        IFS=:
        line=($item)
        case ${line[0]} in
            current-version)
                current_version=${line[1]}
                ;;
        esac
    done
    IFS=$' \t\n'
}

check() {
    local version_data=$(curl -s https://download.libsodium.org/libsodium/releases/ | awk -F '"' '/libsodium/{print $2}' | grep 'tar.gz$'  | awk -F '-' '{print $2}' | awk -F '.' '{printf("%d.%d.%d\n", $1, $2, $3)}')
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
    get_latest_version $data
}

get_latest_version() {
    declare -a sorted_data
    sorted_data=($(for item in ${data[@]}; do echo $item; done | sort -nur))
    version_data=${sorted_data[0]}
    local right=`expr $version_data % 1000`
    local middle=`expr $version_data / 1000 % 1000`
    local left=`expr $version_data / 1000000`
    latest_version="$left.$middle.$right"
}

yum-dependence() {
    yum -y update
    yum -y install m2crypto gcc make
}

apt-dependence() {
    apt-get -y update
    apt-get -y install gcc build-essential python-m2crypto
}

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

init_environment() {
    if [ 1 -eq $distribution ];then
        yum-dependence
    elif [ 0 -eq $distribution ];then
        apt-dependence
    fi
}

install() {
    curl -s https://download.libsodium.org/libsodium/releases/libsodium-$latest_version.tar.gz -o libsodium.tar.gz
    tar -zvxf libsodium.tar.gz
    cd libsodium-$latest_version
    ./configure
    make && make install
    echo "include ld.so.conf.d/*.conf" > /etc/ld.so.conf
    echo "/lib" >> /etc/ld.so.conf
    echo "/usr/lib64" >> /etc/ld.so.conf
    echo "/usr/local/lib" >> /etc/ld.so.conf
    ldconfig
    cd ..
    rm libsodium.tar.gz
    rm -rf libsodium-$latest_version
    sed -i 's/current-version:.*;/current-version:'$latest_version';/g' ./auto-chacha20-config
}

get_shell_config
check
if [ "$latest_version" != "$current_version" ];then
    check_distribution
    distribution=$?
    init_environment
    install
fi
```

本文由 [Eason Yang](https://easonyang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://easonyang.com/about/)。