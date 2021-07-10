---
title: Mybatis的*Mapper.xml文件在Intellij Idea项目中的正确位置
alias: the-right-location-of-mapper-xml-for-mybatis-in-intellijidea
date: 2016-08-13 22:50:32
updated: 2016-08-13 22:50:32
tags: [Java, Intellij Idea, Mybatis,工具]
categories: [教程]
keywords: [Mybatis, xml配置文件, Java, Intellij Idea, 工具, maven, 教程]
---

## 探索

在 Intellij Idea 中使用 Mybatis 时，配置好了 *Mapper.xml 文件，但是编译后却报错找不到 Mapper 接口中的方法，错误类似于 `BindingException: Invalid bound statement` 。由于 Intellij Idea 目前并不支持在 Project Structure 中直接管理 Mybatis 的配置文件，所以只好手动排查问题。

起初以为是 Mapper 接口因为路径配置错误等原因无法正确编译，但是项目编译后的 target 目录下却能找到每个接口编译后的 class 文件。这时一眼扫到 target 目录下竟然完全找不到 *Mapper.xml 。几经查找原因，基本排除了代码和配置问题。联想到之前在 Intellij Idea 下用 Spring 的相关组件做单元测试的时候，总是要把放在 WEB-INF 下的几个配置文件拷贝到 Resources 目录下，才能成功进行测试，否则会报无法加载 xml 文件的错误，于是我猜测 *Mapper.xml 是否也应该放在 Resources 目录下呢？<!--more-->

[JetBrains 公司的文档](https://www.jetbrains.com/help/idea/2016.2/resource-files.html)这样写道：

> When building an application, IntelliJ IDEA copies all resources into the output directory, preserving the directory structure of the resources, relative to the source path. The following file types are recognized as resources by default:
>
> - `.properties`
> - `.xml`
> - `.html`
> - `.dtd`
> - `.tld`
> - `.gif`
> - `.png`
> - `.jpeg`
> - `.jpg`

虽然没有说明在其他目录下这些文件不会编译输出，但是可以确定的是将这些默认类型的文件放到 Resources 目录下是绝对可以编译输出的。所以我尝试将 *Mapper.xml 从 WEB-INF 下移动到 Resources 后，清理 target 目录内容，重新编译，果然不再报之前的错误，但还需要在 Mybatis 的配置文件中配置好 Mapper.xml 的位置才能正常使用：

```xml
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <property name="configLocation" value="WEB-INF/mybatis.xml" />
        <property name="mapperLocations" value="classpath:mapper/*Mapper.xml" />
    </bean>
```

## 后记

此项目按照之前的配置是可以在 Eclipse 系 IDE 中正常使用的，但是如果移植到 Intellij Idea 中则需要做一些改动。根据 https://stackoverflow.com/questions/31076576/xml-files-are-not-copied-to-target-intellij-idea 中的一些回答，对于 Maven 和 Gradle 来说 xml 等文件的位置有可能是硬性规定。所以为了方便起见，使用 Intellij Idea 的项目应该尽量将 IDE 无法自动识别载入到 Project Structure 中的配置文件和其他类型的文件放到 Resources 目录下。除了上述默认的几种类型，还可以通过[这里的说明](https://www.jetbrains.com/help/idea/2016.2/compiler.html)添加其他类型。