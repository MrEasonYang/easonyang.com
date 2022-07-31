---
title: GIA线路VPS评测
url: 2021/01/31/gia-vps-benchmark
date: 2021-01-31 01:22:18
updated: 2021-01-31 01:22:18
tags: [Linux]
categories: [reviews]
keywords: [搬瓦工, SoftBank, linux, benchmark, 测评, 软银]
---
最近折腾个新项目，原价入了某工的 LA GIA 线路机器，这么贵的机器自然是要简单测评记录下的。
<!--more-->
#### benchmark
```
 Superbench.sh -- https://www.oldking.net/350.html
 Mode  : Standard    Version : 1.1.7
 Usage : wget -qO- sb.oldking.net | bash
----------------------------------------------------------------------
 CPU Model            : QEMU Virtual CPU version (cpu64-rhel6)
 CPU Cores            : 2 Cores 2699.998 MHz x86_64
 CPU Cache            : 16384 KB
 OS                   : Ubuntu 20.04 LTS (64 Bit) KVM
 Kernel               : 5.4.0-26-generic
 Total Space          : 4.8 GB / 20.8 GB
 Total RAM            : 193 MB / 995 MB (639 MB Buff)
 Total SWAP           : 12 MB / 259 MB
 Uptime               : 2 days 11 hour 29 min
 Load Average         : 0.43, 0.17, 0.06
 TCP CC               : bbr
 ASN & ISP            : AS25820, IT7 Networks Inc
 Organization         : Cluster Logic Inc
 Location             : Los Angeles, United States / US
 Region               : California
----------------------------------------------------------------------
 I/O Speed( 1.0GB )   : 203 MB/s
 I/O Speed( 1.0GB )   : 315 MB/s
 I/O Speed( 1.0GB )   : 265 MB/s
 Average I/O Speed    : 261.0 MB/s
----------------------------------------------------------------------
 Node Name        Upload Speed      Download Speed      Latency
 Speedtest.net    2875.38 Mbit/s    3365.62 Mbit/s      0.56 ms
 Fast.com         0.00 Mbit/s       150.0 Mbit/s        -
 Nanjing 5G   CT  574.32 Mbit/s     1805.98 Mbit/s      133.18 ms
 Hefei 5G     CT  514.55 Mbit/s     839.72 Mbit/s       134.66 ms
 Guangzhou 5G CT  210.60 Mbit/s     1760.13 Mbit/s      162.12 ms
 TianJin 5G   CU  354.72 Mbit/s     4.65 Mbit/s         148.78 ms
 Shanghai 5G  CU  592.41 Mbit/s     1324.02 Mbit/s      127.44 ms
 Guangzhou 5G CU  64.41 Mbit/s      1942.66 Mbit/s      181.38 ms
 Tianjin 5G   CM  368.53 Mbit/s     325.52 Mbit/s       240.38 ms
 Wuxi 5G      CM  341.23 Mbit/s     368.16 Mbit/s       172.22 ms
 Nanjing 5G   CM  93.98 Mbit/s      51.13 Mbit/s        175.96 ms
 Hefei 5G     CM  278.21 Mbit/s     359.62 Mbit/s       272.16 ms
----------------------------------------------------------------------
 Finished in  : 6 min 12 sec
 Timestamp    : 2021-01-31 01:24:24 GMT+8
 Results      : ./superbench.log
```
#### TraceRoute 等信息
```
LemonBench Linux System Benchmark Utility Version 20201005 Intl BetaVersion 
 
 Bench Start Time:	2021-02-05 18:21:47
 Bench Finish Time:	2021-02-05 18:26:59
 Test Mode:		Fast Mode
 
 -> System Information
 
 OS Release:		Ubuntu 20.04 LTS (Focal Fossa)  (x86_64)
 CPU Model:		QEMU Virtual CPU version (cpu64-rhel6)  2.70 GHz
 CPU Cache Size:	16384 KB
 CPU Number:		2 vCPU
 Virt Type:		KVM
 Memory Usage:		315.23 MB / 995.52 MB
 Swap Usage:		13.50 MB / 260.00 MB
 Disk Usage:		4.33 GB / 20.17 GB
 Boot Device:		/dev/sda3
 Load (1/5/15min):	0.68 0.19 0.06 
 CPU Usage:		6.0% used, 0.0% iowait, 0.0% steal
 Kernel Version:	5.4.0-26-generic
 Network CC Method:	bbr + fq

 -> Network Information

 IPV4 - IP Address:	[US] *.*.*.*
 IPV4 - ASN Info:	25820 (IT7NET - IT7 Networks Inc, CA)
 IPV4 - Region:		United States California Los Angeles

 -> Media Unlock Test

 HBO Now:				Yes
 Bahamut Anime:				No
 Abema.TV:				No
 Princess Connect Re:Dive Japan:	No
 BBC:					No
 Bilibili China Mainland Only:		No
 Bilibili Hongkong/Macau/Taiwan:	No
 Bilibili Taiwan Only:			No

 -> CPU Performance Test (Fast Mode, 1-Pass @ 5sec)

 1 Thread Test:			699 Scores
 2 Threads Test:		1342 Scores

 -> Memory Performance Test (Fast Mode, 1-Pass @ 5sec)

 1 Thread - Read Test :		13358.33 MB/s
 1 Thread - Write Test:		11219.29 MB/s

 -> Disk Speed Test (4K Block/1M Block, Direct Mode)

 Test Name		Write Speed				Read Speed
 100MB-4K Block		5.4 MB/s (1309 IOPS, 19.55 s)		5.2 MB/s (1263 IOPS, 20.26 s)
 1GB-1M Block		156 MB/s (148 IOPS, 6.72 s)		534 MB/s (509 IOPS, 1.96 s)

 -> Speedtest.net Network Speed Test

 Node Name			Upload Speed	Download Speed	Ping Latency	Server Name
 Speedtest Default		461.31 MB/s	261.93 MB/s	0.89 ms		Frontier (United States Los Angeles, CA)
 China, Beijing CU  		58.54 MB/s	140.55 MB/s	150.89 ms	Beijing Unicom (China Beijing)
 China, Shanghai CT 		55.15 MB/s	268.74 MB/s	131.31 ms	China Telecom (China Shanghai)
 China, Hangzhou CM 		41.08 MB/s	53.98 MB/s	162.07 ms	China Mobile Group Zhejiang Co.,Ltd (China Hangzhou)

 -> Traceroute Test (IPV4)


Traceroute to China, Beijing CU (TCP Mode, Max 30 Hop)
============================================================
traceroute to 123.125.99.1 (123.125.99.1), 30 hops max, 60 byte packets
 1  172.22.62.200  3.84 ms  *  LAN Address
 2  172.22.61.48  11.00 ms  *  LAN Address
 3  218.30.49.141  1.47 ms  AS4134  United States California Los Angeles ChinaTelecom
 4  59.43.246.237  127.50 ms  *  China Shanghai ChinaTelecom
 5  59.43.247.57  160.19 ms  *  China Shanghai ChinaTelecom
 6  59.43.138.57  130.24 ms  *  China Shanghai ChinaTelecom
 7  59.43.80.141  132.82 ms  *  China Shanghai ChinaTelecom
 8  202.97.97.233  149.70 ms  AS4134  China Beijing ChinaTelecom
 9  202.97.91.93  157.24 ms  AS4134  China Beijing ChinaTelecom
10  219.158.41.21  155.78 ms  AS4837  China Beijing ChinaUnicom
11  219.158.3.77  154.09 ms  AS4837  China Beijing ChinaUnicom
12  125.33.186.22  153.67 ms  AS4808  China Beijing ChinaUnicom
13  61.148.7.82  154.52 ms  AS4808  China Beijing ChinaUnicom
14  61.148.158.102  160.78 ms  AS4808  China Beijing ChinaUnicom
15  61.135.113.158  151.75 ms  AS4808  China Beijing ChinaUnicom
16  *
17  *
18  123.125.99.1  153.55 ms  AS4808  China Beijing ChinaUnicom


Traceroute to China, Beijing CT (TCP Mode, Max 30 Hop)
============================================================
traceroute to 180.149.128.9 (180.149.128.9), 30 hops max, 60 byte packets
 1  172.22.62.200  59.04 ms  *  LAN Address
 2  172.22.61.48  7.38 ms  *  LAN Address
 3  *
 4  218.30.48.21  0.73 ms  AS4134  United States California Los Angeles ChinaTelecom
 5  59.43.246.237  127.87 ms  *  China Shanghai ChinaTelecom
 6  59.43.182.225  173.36 ms  *  China ChinaTelecom
 7  *
 8  59.43.132.13  156.52 ms  *  China Beijing ChinaTelecom
 9  *
10  36.110.244.46  164.41 ms  AS23724  China Beijing ChinaTelecom
11  *
12  180.149.128.9  162.99 ms  AS23724  China Beijing ChinaTelecom


Traceroute to China, Beijing CM (TCP Mode, Max 30 Hop)
============================================================
traceroute to 211.136.25.153 (211.136.25.153), 30 hops max, 60 byte packets
 1  172.22.62.200  1.63 ms  *  LAN Address
 2  172.22.61.48  4.43 ms  *  LAN Address
 3  218.30.49.177  1.08 ms  AS4134  United States California Los Angeles ChinaTelecom
 4  59.43.246.237  127.78 ms  *  China Shanghai ChinaTelecom
 5  59.43.246.217  128.54 ms  *  China Shanghai ChinaTelecom
 6  59.43.130.217  135.20 ms  *  China Shanghai ChinaTelecom
 7  59.43.80.141  128.92 ms  *  China Shanghai ChinaTelecom
 8  202.97.97.237  151.20 ms  AS4134  China Beijing ChinaTelecom
 9  202.97.57.138  152.65 ms  AS4134  China Beijing ChinaTelecom
10  *
11  *
12  *
13  111.24.3.18  176.98 ms  AS9808  China Beijing ChinaMobile
14  *
15  *
16  211.136.67.166  221.09 ms  AS56048  China Beijing ChinaMobile
17  211.136.95.226  226.34 ms  AS56048  China Beijing ChinaMobile
18  *
19  211.136.25.153  220.10 ms  AS56048  China Beijing ChinaMobile


Traceroute to China, Shanghai CU (TCP Mode, Max 30 Hop)
============================================================
traceroute to 58.247.8.158 (58.247.8.158), 30 hops max, 60 byte packets
 1  172.22.62.200  1.11 ms  *  LAN Address
 2  172.22.61.48  5.31 ms  *  LAN Address
 3  *
 4  218.30.49.93  1.02 ms  AS4134  United States ChinaTelecom
 5  59.43.181.145  124.37 ms  *  China Shanghai ChinaTelecom
 6  59.43.246.213  130.75 ms  *  China Shanghai ChinaTelecom
 7  59.43.138.53  130.65 ms  *  China Shanghai ChinaTelecom
 8  219.158.38.241  130.86 ms  AS4837  China Beijing ChinaUnicom
 9  219.158.9.97  138.60 ms  AS4837  China Shanghai ChinaUnicom
10  *
11  139.226.228.46  195.64 ms  AS17621  China Shanghai ChinaUnicom
12  139.226.225.22  131.00 ms  AS17621  China Shanghai ChinaUnicom
13  58.247.8.153  144.38 ms  AS17621  China Shanghai ChinaUnicom
14  *
15  *
16  *
17  *
18  *
19  *
20  *
21  *
22  *
23  *
24  *
25  *
26  *
27  *
28  *
29  *
30  *


Traceroute to China, Shanghai CT (TCP Mode, Max 30 Hop)
============================================================
traceroute to 180.153.28.5 (180.153.28.5), 30 hops max, 60 byte packets
 1  172.22.62.200  9.28 ms  *  LAN Address
 2  172.22.61.48  2.67 ms  *  LAN Address
 3  193.41.248.189  1.24 ms  AS54574  United States California Los Angeles dmit.io
 4  193.41.248.130  1.15 ms  AS54574  United States California Los Angeles dmit.io
 5  59.43.184.153  127.07 ms  *  China Shanghai ChinaTelecom
 6  59.43.187.69  128.11 ms  *  China Shanghai ChinaTelecom
 7  59.43.130.209  134.81 ms  *  China Shanghai ChinaTelecom
 8  59.43.80.141  128.42 ms  *  China Shanghai ChinaTelecom
 9  101.95.88.1  129.58 ms  AS4812  China Shanghai ChinaTelecom
10  *
11  101.95.225.222  129.12 ms  AS4811  China Shanghai ChinaTelecom
12  101.227.255.46  130.74 ms  AS4812  China Shanghai ChinaTelecom
13  180.153.28.5  130.07 ms  AS4812  China Shanghai ChinaTelecom


Traceroute to China, Shanghai CM (TCP Mode, Max 30 Hop)
============================================================
traceroute to 221.183.55.22 (221.183.55.22), 30 hops max, 60 byte packets
 1  172.22.62.200  11.03 ms  *  LAN Address
 2  172.22.61.48  12.38 ms  *  LAN Address
 3  193.41.248.189  2.25 ms  AS54574  United States California Los Angeles dmit.io
 4  193.41.248.130  1.28 ms  AS54574  United States California Los Angeles dmit.io
 5  59.43.181.145  124.50 ms  *  China Shanghai ChinaTelecom
 6  59.43.187.69  125.66 ms  *  China Shanghai ChinaTelecom
 7  59.43.138.49  129.86 ms  *  China Shanghai ChinaTelecom
 8  59.43.80.102  131.45 ms  *  China Shanghai ChinaTelecom
 9  202.97.46.66  129.61 ms  AS4134  China Shanghai ChinaTelecom
10  *
11  221.183.86.106  160.70 ms  AS9808  China ChinaMobile
12  111.24.4.74  161.36 ms  AS9808  China ChinaMobile
13  221.176.17.218  157.10 ms  AS9808  China Shanghai ChinaMobile
14  221.183.55.22  249.60 ms  AS9808  China Shanghai ChinaMobile


Traceroute to China, Guangzhou CU (TCP Mode, Max 30 Hop)
============================================================
traceroute to 210.21.4.130 (210.21.4.130), 30 hops max, 60 byte packets
 1  172.22.62.200  10.27 ms  *  LAN Address
 2  172.22.61.48  5.18 ms  *  LAN Address
 3  *
 4  218.30.49.93  1.09 ms  AS4134  United States ChinaTelecom
 5  59.43.182.106  160.67 ms  *  China Guangdong Guangzhou ChinaTelecom
 6  *
 7  59.43.130.101  166.71 ms  *  China Guangdong Guangzhou ChinaTelecom
 8  219.158.38.245  168.73 ms  AS4837  China Beijing ChinaUnicom
 9  219.158.10.249  170.08 ms  AS4837  China Guangdong Guangzhou ChinaUnicom
10  112.91.0.254  174.06 ms  AS17816  China Guangdong Guangzhou ChinaUnicom
11  120.80.170.250  165.64 ms  AS17622  China Guangdong Guangzhou ChinaUnicom
12  *
13  *
14  *
15  *
16  *
17  *
18  *
19  *
20  *
21  *
22  *
23  *
24  *
25  *
26  *
27  *
28  *
29  *
30  *


Traceroute to China, Guangzhou CT (TCP Mode, Max 30 Hop)
============================================================
traceroute to 113.108.209.1 (113.108.209.1), 30 hops max, 60 byte packets
 1  172.22.62.200  15.66 ms  *  LAN Address
 2  172.22.61.48  45.10 ms  *  LAN Address
 3  *
 4  218.30.49.89  1.23 ms  AS4134  United States California Los Angeles ChinaTelecom
 5  59.43.182.102  161.20 ms  *  United States California Los Angeles ChinaTelecom
 6  *
 7  59.43.130.101  170.55 ms  *  China Guangdong Guangzhou ChinaTelecom
 8  202.97.43.81  169.50 ms  AS4134  China Zhejiang Hangzhou ChinaTelecom
 9  113.108.209.1  168.12 ms  AS58466  China Guangdong Guangzhou ChinaTelecom


Traceroute to China, Guangzhou CM (TCP Mode, Max 30 Hop)
============================================================
traceroute to 120.196.212.25 (120.196.212.25), 30 hops max, 60 byte packets
 1  172.22.62.200  9.70 ms  *  LAN Address
 2  172.22.61.48  9.98 ms  *  LAN Address
 3  193.41.248.189  0.72 ms  AS54574  United States California Los Angeles dmit.io
 4  193.41.248.130  2.39 ms  AS54574  United States California Los Angeles dmit.io
 5  59.43.182.106  160.50 ms  *  China Guangdong Guangzhou ChinaTelecom
 6  *
 7  59.43.130.153  166.76 ms  *  China Guangdong Guangzhou ChinaTelecom
 8  59.43.80.122  171.05 ms  *  China Guangdong Guangzhou ChinaTelecom
 9  202.97.63.206  167.81 ms  AS4134  China Guangdong Guangzhou ChinaTelecom
10  221.183.65.173  168.33 ms  AS9808  China Guangdong Guangzhou ChinaMobile
11  221.176.22.133  167.83 ms  AS9808  China Guangdong Guangzhou ChinaMobile
12  111.24.4.253  170.29 ms  AS9808  China Guangdong Guangzhou ChinaMobile
13  111.24.5.218  171.07 ms  AS9808  China Guangdong Guangzhou ChinaMobile
14  211.136.196.238  168.76 ms  AS56040  China Guangdong Guangzhou ChinaMobile
15  211.136.208.121  170.02 ms  AS56040  China Guangdong Guangzhou ChinaMobile
16  211.139.180.110  175.44 ms  AS56040  China Guangdong Guangzhou ChinaMobile
17  120.196.212.25  264.21 ms  AS56040  China Guangdong Guangzhou ChinaMobile


Traceroute to China, Shanghai CU AS9929 (TCP Mode, Max 30 Hop)
============================================================
traceroute to 210.13.66.238 (210.13.66.238), 30 hops max, 60 byte packets
 1  172.22.62.200  3.36 ms  *  LAN Address
 2  172.22.61.48  1.63 ms  *  LAN Address
 3  218.30.49.141  1.01 ms  AS4134  United States California Los Angeles ChinaTelecom
 4  59.43.189.33  130.63 ms  *  China Shanghai ChinaTelecom
 5  59.43.246.213  130.66 ms  *  China Shanghai ChinaTelecom
 6  59.43.130.217  128.99 ms  *  China Shanghai ChinaTelecom
 7  219.158.40.173  129.93 ms  AS4837  China Shanghai ChinaUnicom
 8  219.158.41.14  149.12 ms  AS4837  China Shanghai ChinaUnicom
 9  210.13.112.254  156.57 ms  AS9929  China Shanghai ChinaUnicom
10  210.13.66.237  162.50 ms  AS9929  China Shanghai ChinaUnicom
11  *
12  210.13.66.238  152.86 ms  AS9929  China Shanghai ChinaUnicom


Traceroute to China, Shanghai CT CN2 (TCP Mode, Max 30 Hop)
============================================================
traceroute to 58.32.0.1 (58.32.0.1), 30 hops max, 60 byte packets
 1  172.22.62.200  11.34 ms  *  LAN Address
 2  172.22.61.48  11.88 ms  *  LAN Address
 3  *
 4  218.30.49.109  2.24 ms  AS4134  United States ChinaTelecom
 5  59.43.184.153  127.24 ms  *  China Shanghai ChinaTelecom
 6  59.43.187.85  131.27 ms  *  China Shanghai ChinaTelecom
 7  59.43.138.49  128.62 ms  *  China Shanghai ChinaTelecom
 8  101.95.88.210  135.30 ms  AS4812  China Shanghai ChinaTelecom
 9  101.95.40.114  128.74 ms  AS4812  China Shanghai ChinaTelecom
10  58.32.0.1  131.83 ms  AS4812  China Shanghai ChinaTelecom


Traceroute to China, Guangzhou CT CN2 Gaming Broadband (TCP Mode, Max 30 Hop)
============================================================
traceroute to 119.121.0.1 (119.121.0.1), 30 hops max, 60 byte packets
 1  172.22.62.200  1.43 ms  *  LAN Address
 2  172.22.61.48  6.81 ms  *  LAN Address
 3  *
 4  218.30.49.109  0.87 ms  AS4134  United States ChinaTelecom
 5  59.43.246.233  165.29 ms  *  China Guangdong Guangzhou ChinaTelecom
 6  *
 7  59.43.130.149  170.22 ms  *  China Guangdong Guangzhou ChinaTelecom
 8  183.57.161.10  166.93 ms  AS134773  China Guangdong Guangzhou ChinaTelecom
 9  *
10  *
11  *
12  *
13  *
14  *
15  *
16  *
17  *
18  *
19  *
20  *
21  *
22  *
23  *
24  *
25  *
26  *
27  *
28  *
29  *
30  *


Traceroute to China, Beijing Dr.Peng Home Network (TCP Mode, Max 30 Hop)
============================================================
traceroute to 14.131.128.1 (14.131.128.1), 30 hops max, 60 byte packets
 1  172.22.62.200  7.74 ms  *  LAN Address
 2  172.22.61.48  3.03 ms  *  LAN Address
 3  218.30.49.141  0.88 ms  AS4134  United States California Los Angeles ChinaTelecom
 4  59.43.182.81  138.35 ms  *  United States California Los Angeles ChinaTelecom
 5  59.43.187.61  130.55 ms  *  China Shanghai ChinaTelecom
 6  *
 7  59.43.80.82  130.81 ms  *  China Shanghai ChinaTelecom
 8  202.97.56.161  161.48 ms  AS4134  China Beijing ChinaTelecom
 9  202.97.88.250  150.64 ms  AS4134  China Beijing ChinaTelecom
10  219.158.44.109  156.56 ms  AS4837  China Beijing ChinaUnicom
11  *
12  61.149.203.182  151.00 ms  AS4808  China Beijing ChinaUnicom
13  202.96.13.182  150.21 ms  AS4808  China Beijing ChinaUnicom
14  *
15  *
16  *
17  *
18  *
19  *
20  *
21  *
22  *
23  *
24  *
25  *
26  *
27  *
28  *
29  *
30  *


Traceroute to China, Beijing Dr.Peng Network IDC Network (TCP Mode, Max 30 Hop)
============================================================
traceroute to 211.167.230.100 (211.167.230.100), 30 hops max, 60 byte packets
 1  172.22.62.200  9.87 ms  *  LAN Address
 2  172.22.61.48  9.26 ms  *  LAN Address
 3  218.30.49.141  11.01 ms  AS4134  United States California Los Angeles ChinaTelecom
 4  59.43.182.81  130.19 ms  *  United States California Los Angeles ChinaTelecom
 5  59.43.187.61  132.88 ms  *  China Shanghai ChinaTelecom
 6  59.43.130.209  134.83 ms  *  China Shanghai ChinaTelecom
 7  59.43.80.141  129.02 ms  *  China Shanghai ChinaTelecom
 8  *
 9  *
10  219.158.44.117  154.80 ms  AS4837  China Beijing ChinaUnicom
11  219.158.5.5  163.65 ms  AS4837  China Beijing ChinaUnicom
12  123.126.0.126  149.84 ms  AS4808  China Beijing ChinaUnicom
13  202.96.13.114  153.53 ms  AS4808  China Beijing ChinaUnicom
14  61.51.37.206  154.31 ms  AS4808  China Beijing ChinaUnicom
15  218.241.244.10  150.18 ms  AS4808  China Beijing DRPENG
16  124.205.97.138  155.60 ms  AS4808  China Beijing DRPENG
17  218.241.253.130  152.15 ms  AS4808  China Beijing DRPENG
18  211.167.230.100  154.86 ms  AS17964  China Beijing DRPENG


Traceroute to China, Beijing CERNET (TCP Mode, Max 30 Hop)
============================================================
traceroute to 202.205.109.205 (202.205.109.205), 30 hops max, 60 byte packets
 1  172.22.62.200  9.54 ms  *  LAN Address
 2  172.22.61.48  1.19 ms  *  LAN Address
 3  193.41.248.189  3.05 ms  AS54574  United States California Los Angeles dmit.io
 4  193.41.248.130  1.13 ms  AS54574  United States California Los Angeles dmit.io
 5  59.43.184.157  124.83 ms  *  China Shanghai ChinaTelecom
 6  59.43.187.65  146.16 ms  *  China Shanghai ChinaTelecom
 7  59.43.130.209  129.37 ms  *  China Shanghai ChinaTelecom
 8  59.43.80.138  134.11 ms  *  China Shanghai ChinaTelecom
 9  202.97.46.10  130.25 ms  AS4134  China Shanghai ChinaTelecom
10  *
11  101.4.117.30  181.05 ms  AS4538  China Beijing CHINAEDU
12  101.4.116.118  187.70 ms  AS4538  China Beijing CHINAEDU
13  *
14  101.4.113.110  176.57 ms  AS4538  China Beijing CHINAEDU
15  219.224.102.230  180.50 ms  AS4538  China Beijing CHINAEDU
16  202.112.38.158  180.58 ms  AS4538  China Beijing CHINAEDU
17  202.205.109.205  177.33 ms  AS4538  China Beijing CHINAEDU


Traceroute to China, Beijing CSTNET (TCP Mode, Max 30 Hop)
============================================================
traceroute to 159.226.254.1 (159.226.254.1), 30 hops max, 60 byte packets
 1  172.22.62.200  1.52 ms  *  LAN Address
 2  172.22.61.48  11.33 ms  *  LAN Address
 3  193.41.248.189  0.66 ms  AS54574  United States California Los Angeles dmit.io
 4  193.41.248.130  1.13 ms  AS54574  United States California Los Angeles dmit.io
 5  59.43.189.37  128.95 ms  *  China Shanghai ChinaTelecom
 6  59.43.187.65  127.32 ms  *  China Shanghai ChinaTelecom
 7  59.43.249.13  165.76 ms  *  China Hong Kong ChinaTelecom
 8  63.218.211.9  155.82 ms  AS3491,AS31713  China Hong Kong pccw.com
 9  59.43.80.98  131.17 ms  *  China Shanghai ChinaTelecom
10  63.217.16.34  154.76 ms  AS3491,AS31713  China Hong Kong pccw.com
11  63.217.16.34  160.24 ms  AS3491,AS31713  China Hong Kong pccw.com
12  159.226.254.1  179.20 ms  AS7497  China Beijing CSTNET


Traceroute to China, Beijing GCable (TCP Mode, Max 30 Hop)
============================================================
traceroute to 211.156.140.17 (211.156.140.17), 30 hops max, 60 byte packets
 1  172.22.62.200  2.64 ms  *  LAN Address
 2  172.22.61.48  7.89 ms  *  LAN Address
 3  *
 4  218.30.48.21  0.66 ms  AS4134  United States California Los Angeles ChinaTelecom
 5  59.43.182.81  129.94 ms  *  United States California Los Angeles ChinaTelecom
 6  59.43.247.125  155.15 ms  *  China Beijing ChinaTelecom
 7  *
 8  59.43.132.13  152.59 ms  *  China Beijing ChinaTelecom
 9  *
10  106.120.252.154  155.94 ms  AS4847  China Beijing ChinaTelecom
11  60.247.93.254  155.39 ms  AS4847  China Beijing ChinaTelecom
12  211.156.128.225  151.06 ms  AS7641  China Beijing CATV
13  211.156.128.85  155.99 ms  AS7641  China Beijing CATV
14  211.156.140.17  154.99 ms  AS7641  China Beijing CATV


Generated by LemonBench on 2021-02-05T18:27:00Z Version 20201005 Intl BetaVersion, Hosted by DMIT.IO
```
#### PING
![](https://gmiimg.com/EiQpZc-1612027638472.png)