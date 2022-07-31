---
title: 解决Intellij Idea下从Maven创建项目时Loading archetype list的问题
url: 2016/08/04/solve-maven-loading-archetype-list-problem-in-intellijidea
date: 2016-08-04 11:16:38
updated: 2016-08-04 11:16:38
tags: [Intellijidea, tool, Java]
categories: [programming]
keywords: [java, intellij idea, maven, loading archetype list]
---

在某些场景下在 Intellij Idea 中使用 Maven 创建项目时会出现一直在 Loding archetype list 的问题，如下图。<!--more-->

![loading-archetype-list](https://gmiimg.com/393934433978ebb24b1a42dbf28652c9.png)

在 Stackoverflow 中查到：[IntelliJ new project - maven archetype list empty](https://stackoverflow.com/questions/27893134/intellij-new-project-maven-archetype-list-empty) 中看到问题应该出在给 Maven 使用的 JRE 内存分配不足。

在设置中将默认的 `-Xmx512m` 中的512改为合适的值即可。

![vm-options-for-importer](https://gmiimg.com/396a2d7a71373d892a2137f2a884e68e.png)
