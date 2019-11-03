---
title: Unix的IO模型中的同步、异步与阻塞、非阻塞
date: 2017-02-28 14:31:18
updated: 2017-02-28 14:31:18
tags:
  - I/O
  - 操作系统
  - Linux
categories:
  - 编程
keywords:
  - 同步
  - 异步
  - 阻塞
  - 非阻塞
  - io
  - unix
  - linux
---

## 前言

同步、异步与阻塞、非阻塞这四个概念在多线程、多进程、I/O 相关编程中非常常见。然而在不同的场景下，定义中给不同框架、操作系统功能所打上的标签常常让不熟悉这四个概念区别的人疑惑不已。本文就从许多应用层框架所依赖的 Unix 中的 5 个 I/O 模型来理解这四个概念。

## Unix 5 大 I/O 模型简述

在 Unix 中，有 5 种 I/O 模型：

- 阻塞式 I/O 
- 非阻塞式 I/O
-  I/O 多路复用
- 信号驱动 I/O
- 异步 I/O

在类 Unix 系统中，I/O 操作总是分为两个阶段：

- 阶段一：内核等待数据到达，并在数据到达后内核将其拷贝到内核的缓冲区
- 阶段二：内核将内核缓冲区中的相应数据拷贝到进程缓冲区

## 同步与异步

结合生活理解这一对概念，我们假设一个场景：<!--more-->

> A公司某经理今天早上上班的时候通知前台接待公司今天要接受一个重要的包裹。

- 同步：前台的接待看着快递把包裹运到了前台，然后打电话告诉经理包裹送到了，经理随即吩咐接待把包裹送到你办公室，挂断电话后不久包裹送上来了，经理拆开包裹开始用包裹里的物品工作。
- 异步：前台的接待看着快递把包裹运到了前台，前台看过「把信送给加西亚」之类的书，深知做事情不能全靠上级吩咐，所以他没有通知经理，而是直接把包裹送到了经理的办公室，经理在这个过程中是不知道包裹何时送到的，随后经理拆开包裹开始用包裹里的物品工作。

对应到 Unix 的 I/O ，经理就是进程（线程），前台接待就是系统内核，前台就是内核的缓冲区，经理办公室则是进程（线程）缓冲区。

可以看到经理在这两个过程中的行为差异在于是否亲自通知接待呈送包裹，而非经理是否亲自取回包裹。所以同步与异步更像是对某一进程（线程）的行为的描述，这两种行为是互斥的。

## 阻塞与非阻塞

阻塞（blocking）与非阻塞（non-blocking）的英文名称中所含有的 blocking 如果直接作为名词来看的话词义如下：

> 舞台场面设计，舞台调度，舞台场面调度设计，模块化

这显然与「阻塞」这个词毫无关系，因此我想我们将 「blocking」视作为 block 的动名词形式恐怕更为合适。而这动名词的词性也揭示了「阻塞」与「非阻塞」的深层含义，那就是这两个概念用来描述某一进程（线程）当前的「运行状态」的。

放下手头工作一直专心等待的进程（线程）我们就说他阻塞了；而虽然心里还在等，但是身体上却诚实地该干嘛干嘛的进程（线程）我们就说他是非阻塞的，这两种状态是互斥的。

##  Unix I/O 模型与四个概念的联系

从五大模型的名称中就可以看出，最后一种「异步 I/O 」是异步的，实际上除了他之外的四种模型全部都是同步的。另一方面，除了「阻塞式 I/O 」名如其人是阻塞的之外，其他四种又都是非阻塞的。

五大模型的比较如下图：

![5-io-model-comparison.png](sync-async-blocking-non-blocking-in-unix-io-model/5-io-model-comparison.png)

可以看到，除了「异步 I/O 模型」在第二阶段

细心的读者此时一定会产生这样两个疑问：

- 前四种模型的第二阶段完全是阻塞状态，为何却只称 「阻塞式 I/O 」为阻塞的？
- 尽管「异步 I/O 模型」在第二阶段没有主动进行拷贝数据到自己缓冲区的动作，但他在第一阶段明明主动发起了 I/O 请求啊，为何却能称其为异步的呢？

仔细思考一下，我们就会发现，本文提到的两对概念，实际上是对应着 I/O 操作的不同阶段的，而非整个 I/O 过程，下面两个解释将回答这两个疑问：

- 模式的「阻塞」或「非阻塞」*定义*取决于进程（线程）在第一阶段是否阻塞，与第二阶段无关。
- 模式的「同步」或「异步」*定义*取决于进程（线程）在第二阶段是同步还是异步的，与第一阶段无关。

## 同步、异步与阻塞、非阻塞之间的关系

正如在 Unix 的五大 I/O 模型中这四个概念的关系一样，尽管我们总是听到前两者中的一个总是与后两者中的一个联合成一个词组使用，但实际上这两对概念之间没有任何必然的联系。

## 后记

尽管执着于概念的意义并不是很大，然而这样的深究对理解各种相关文献却极为重要。本文的内容或许有一定的局限性，但在狭义上是正确无误的，希望读者结合实际分析。

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。