---
title: 在Intellij Idea中使用Maven创建Mybatis&Spring&SpringMVC项目
date: 2016-08-13 23:10:32
updated: 2016-08-13 23:10:32
tags: [Mybatis, Spring, Spring MVC, Java, Intellij Idea, 工具]
categories: [教程]
keywords: [Mybatis, Spring, Spring MVC, Java, Intellij Idea, 工具, maven, 教程]
---

## 创建 Spring & SpringMVC 项目

参考前文：[在Intellij Idea中使用Maven创建Spring&SpringMVC项目](https://eason-yang.com/2016/08/03/use-maven-to-create-a-spring-springmvc-project-in-intellijidea/) 

## 添加 Mybatis Maven 依赖

向 pom.xml 中添加如下内容：

```xml
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.1</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>
```

## 添加 Mybatis 配置信息

首先在 WEB-INF 或 Resources 目录下新建一个 xml 文件，名称随意，这里叫做 mybatis.xml ，内容根据需要配置，示例如下（这里仅配置了实体包的别名扫描，这样就不用写全路径了）：<!--more-->

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name="com.sys.entity" />
    </typeAliases>
</configuration>
```

随后在 Spring 配置文件中加入以下内容，以结合 Spring 的 IoC 和 DI 使用 Mybatis：

```xml
    <context:component-scan base-package="com.sys.service"/>
    <context:component-scan base-package="com.sys.mapper"/>

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"
          destroy-method="close">
        <property name="driverClass" value="com.mysql.jdbc.Driver" />
        <property name="jdbcUrl" value="jdbc:mysql://localhost/test?useUnicode=true&amp;characterEncoding=UTF-8&amp;zeroDateTimeBehavior=convertToNull" />
        <property name="user" value="vexpress" />
        <property name="password" value="vexpress208" />
        <property name="maxPoolSize" value="40" />
        <property name="minPoolSize" value="1" />
        <property name="initialPoolSize" value="1" />
        <property name="maxIdleTime" value="20" />
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <property name="configLocation" value="WEB-INF/mybatis.xml" />
        <property name="mapperLocations" value="classpath:mapper/*Mapper.xml" />
    </bean>

    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.sys.mapper" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    </bean>
```

dataSource 这个 bean 根据需要自行配置，在 class 处指定连接池，然后在下面的属性中配置好数据库连接，当然这里可以用一个 properties 文件来存储数据库配置信息方便复用和管理。

sqlSessionFactory 这个 bean 极为重要，类似于 Hibernate 的 SessionFactory ，是要注入到程序中的，我们要在这里至少配置好数据源和配置文件位置（也就是刚才新建的 mybatis.xml 的位置），而对于 Intellij Idea 来说，还需要配置好配置文件位置（ Eclipse 系非必需步骤），此处有需要注意内容，参见：[Mybatis的*Mapper.xml文件在Intellij Idea项目中的正确位置](https://eason-yang.com/2016/08/13/the-right-location-of-mapper-xml-for-mybatis-in-intellijidea/)

最后在配置 bean 中配置好要扫描的 mapper 接口包和刚才的 sqlSessionFactory。配置文件到此结束。

## 创建一个 Mapper 接口

在 com.sys.mapper 中新建一个接口，定义一些方法：

```java
@Repository
public interface UserMapper {
    User findUser(int id);
}
```

这里需要注意的是，如果你不在这个接口上加上 @Repository 注解，那么当你在 Service 实现类中用 @Autowired 等注解希望注入此 Mapper 时，Intellij Idea 或出现红线报错道找不到这个 bean ，毕竟 Mapper 只是一个接口，并没有实现类。这样做虽然运行时没有问题，但是红线还是很碍眼，而加上这个注解 Intellij Idea 就能找到 Mapper 了。

## 创建 Service

示例如下：

UserService.java

```java
public interface UserService {
    User findUser(int id);
}
```

UserServiceImpl.java

```java
@Service("userService")
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;

    public User findUser(int id) {
        return userMapper.findUser(id);
    }
}
```

## 完成 UserMapper.xml

这里仅进行一个简单的 SELECT 查询：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sys.mapper.user.UserMapper">
    <select id="findUser" parameterType="int" resultType="User">
        SELECT * FROM system_user WHERE id = #{id}
    </select>
</mapper>
```

将上述内容保存到 Resources 目录下，也就是你在配置文件中定义的 Mapper 文件的位置。当然除了使用 xml 进行配置，使用注解的方式直接在 UserMapper 上配置也是完全可以的。

## 测试

至此，Mybatis 已配置完毕，我们只要使用 @Autowired 或 @Resource 注解将 UserService 注入就可以进行数据查询了。

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。