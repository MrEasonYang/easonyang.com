---
title: ifs-in-linux-shell
tags: [Linux, Shell]
categories: 运维
---

IFS(Internal Field Seprator) 即 “内部域分隔符”，是 Linux Shell 用来分隔命令字符串的，默认值为“空格”、“制表符”及“换行符”，也就是默认按照这三个字符进行分割。通过执行 `echo "$IFS" | od -b` 可以查看到八进制的默认值为 040 011 012 ，对应着上面的三个符号。

对应到 Shell Script 中，`$IFS` 则应该等同于 `$' \t\n'` ，这样就意味着，我们可以定义多个分隔符，用 $ 这样的符号来定义分隔符的具体位置。例如：

* 例 1

```sh
#! /bin/bash

data[0]='1,2,3'
data[1]='1-2-3'
data[2]='1,2-3!4'
data[3]='1.2-3/4!5'
IFS=',-!'
for array in ${data[@]}
do
    for item in ${array[@]}
    do
        echo $item
    done
done
```

执行结果为：

> 1
> 2
> 3
> 1
> 2
> 3
> 1
> 2
> 3
> 4
> 1.2
> 3/4
> 5

* 例 2

```

```

