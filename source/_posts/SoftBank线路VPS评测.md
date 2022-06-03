---
title: SoftBank线路VPS评测
alias: 2021/01/22/softbank-vps-benchmark
dir: /
date: 2021-01-22 22:17:38
updated: 2021-01-22 22:17:38
tags:
  - Linux
categories: [评测与体验]
keywords: [搬瓦工, SoftBank, linux, benchmark, 测评, 软银]
---
最近入手了个软银线路的某工的日本机器，此线路是联通友好线路，可惜机器低配高价，性价比很一般。
入手的主要原因是为了能给家里的 NAS 建立个稳定的穿透链路，由于宽带和移动网络使用的都是联通，所以其实整体体验还不错，这里贴一下相关测试，给大家个参考。
<!--more-->
#### benchmark
```
 Superbench.sh -- https://www.oldking.net/350.html
 Mode  : Standard    Version : 1.1.7
 Usage : wget -qO- sb.oldking.net | bash
----------------------------------------------------------------------
 CPU Model            : QEMU Virtual CPU version (cpu64-rhel6)
 CPU Cores            : 1 Cores 2699.998 MHz x86_64
 CPU Cache            : 16384 KB
 OS                   : CentOS 7.9.2009 (64 Bit) KVM
 Kernel               : 4.10.4-1.el7.elrepo.x86_64
 Total Space          : 3.0 GB / 9.8 GB
 Total RAM            : 236 MB / 503 MB (227 MB Buff)
 Total SWAP           : 64 MB / 131 MB
 Uptime               : 2 days 17 hour 30 min
 Load Average         : 0.02, 0.03, 0.00
 TCP CC               : bbr
 ASN & ISP            : AS25820, IT7 Networks Inc
 Organization         : Cluster Logic Inc
 Location             : Osaka, Japan / JP
 Region               : Ōsaka
----------------------------------------------------------------------
 I/O Speed( 1.0GB )   : 286 MB/s
 I/O Speed( 1.0GB )   : 361 MB/s
 I/O Speed( 1.0GB )   : 334 MB/s
 Average I/O Speed    : 327.0 MB/s
----------------------------------------------------------------------
 Node Name        Upload Speed      Download Speed      Latency
 Speedtest.net    520.22 Mbit/s     514.72 Mbit/s       8.21 ms
 Fast.com         0.00 Mbit/s       167.3 Mbit/s        -
 Nanjing 5G   CT  2149.70 Mbit/s    1545.39 Mbit/s      32.67 ms
 Hefei 5G     CT  498.22 Mbit/s     1873.07 Mbit/s      55.50 ms
 Guangzhou 5G CT  0.27 Mbit/s       754.22 Mbit/s       78.12 ms
 TianJin 5G   CU  1457.69 Mbit/s    2082.64 Mbit/s      51.30 ms
 Shanghai 5G  CU  1423.34 Mbit/s    1103.91 Mbit/s      52.21 ms
 Guangzhou 5G CU  1127.63 Mbit/s    1582.78 Mbit/s      71.74 ms
 Tianjin 5G   CM  136.31 Mbit/s     854.07 Mbit/s       105.92 ms
 Wuxi 5G      CM  76.56 Mbit/s      873.37 Mbit/s       115.29 ms
 Nanjing 5G   CM  1.50 Mbit/s       564.69 Mbit/s       240.60 ms
 Hefei 5G     CM  819.74 Mbit/s     989.33 Mbit/s       96.66 ms
----------------------------------------------------------------------
 Finished in  : 5 min 56 sec
 Timestamp    : 2021-01-22 18:31:11 GMT+8
 Results      : ./superbench.log
```
#### PING
![](https://gmiimg.com/6Nk4xr-1611895568649.png)
#### MTR
![](https://gmiimg.com/swikzL-1611895593836.png)
