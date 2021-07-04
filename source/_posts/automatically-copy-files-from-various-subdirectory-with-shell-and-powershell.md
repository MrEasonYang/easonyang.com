---
title: 使用PowerShell及Shell实现将子目录中的所有指定类型文件批量复制到其他目录
date: 2016-07-03 14:36:50
tags: [Linux, Windows, Shell, PowerShell]
categories: 运维
---

最近有个小需求，需要在 windows 系统下，把 mobi 格式的电子书文件从不同子目录里拷贝到同一目录中，由于微软的 Bash for windows 还没出正式版，所以这里先用 PowerShell 解决。

首先用 Dir 命令遍历所有子目录获取 mobi 文件：

`$data = Dir C:\data -filter *.mobi -recurse`

-recurse 参数会递归地遍历 C:\data 下的子目录，寻找 -filter 所定义的所有以 mobi 为后缀的文件，将结果存入变量 data 中。<!--more-->

随后用 Foreach-Object 循环遍历 data 变量，执行复制文件的操作：

```powershell
$data | Foreach-Object{
    echo $_.Name
    Copy-Item $_.FullName D:\result
}
```

为了使这个脚本具有通用性，将上文几个路径及后缀定义为用户可以输入的参数，最后脚本如下：

```powershell
param($From, $To, $Suffix)
$data = Dir $From -filter *.$Suffix -recurse
$data | Foreach-Object{
    echo $_.Name
    Copy-Item $_.FullName $To
}
```

保存为 auto-copy.ps1 文件后，执行的时候会遇到些麻烦。如果在 Linux 下，我们一般 chmod +x 然后 ./*.sh 即可，但是由于 PowerShell 出于安全考虑默认禁止直接运行脚本，所以直接运行 ./auto-copy.ps1 会报类似 cannot be loaded because running scripts is disabled on this system 这样的错误。解决的方法有两个，一种是修改 Set-ExecutionPolicy 为 RemoteSigned、AllSigned、Unrestricted 中的一个（各个策略的含义参考 https://technet.microsoft.com/en-us/library/ee176961.aspx ），但是会存在潜在的安全问题。另一种是以 powershell -ExecutionPolicy ByPass -File '.\auto-copy.ps1'  这样的形式执行脚本。在这里我们选择后者，执行以下命令即可：

`powershell -ExecutionPolicy ByPass -File '.\auto-copy.ps1' -From C:\data -To D:\result -Suffix mobi`

举一反三，下面这个脚本在 Linux 下使用 Shell 实现相同的功能：

```sh
#! /bin/bash

from=
to=
suffix=
while getopts "f:t:s:" option
do
    case $option in
        f)
            from=$OPTARG
            ;;
        t)
            to=$OPTARG
            ;;
        s)
            suffix=$OPTARG
            ;;
        \?)
            echo "Invalid option: -$OPTARG" >&2
            ;;
    esac
done

data=($(find $from -name *.$suffix))
for item in ${data[@]}
do
    echo $item
    cp $item $to
done
```

和 PowerShell 的实现差不多，主要区别在于传参的处理上。