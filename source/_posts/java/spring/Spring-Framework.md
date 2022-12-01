---
title: Spring 框架模块
description: Spring 框架模块介绍
categories: 
- Java
- Spring
---

Spring Framework 大概由20个模块组成:

![spring-module-1](https://user-images.githubusercontent.com/7795335/100718377-3b9c4300-33f6-11eb-89aa-03a8bab64373.png)

### Core Container

Core Container 由`spring-core`,`spring-beans`,`spring-context`,`spring-context-support`,`spring-expression`(Spring expression language)模块组成。

- `spring-core`和`spring-beans`提供了IOC和DI的实现，是整个框架的基础。

- `spring-context`:它提供一个框架式的对象访问方式，类似于一个`JNDI`注册表。上下文模块从`Beans`模块继承其功能，并添加支持国际化（使用，例如，资源集合），事件传播，资源负载，并且透明创建上下文，例如，`Servlet`容器。`Context`模块还支持`Java EE`的功能，如`EJB，JMX`和基本的远程处理。`ApplicationContext`接口是`Context`模块的焦点。 `spring-context-support`支持整合普通第三方库到`Spring`应用程序上下文，特别是用于高速缓存（`ehcache`，`JCache`）和调度（`CommonJ`，`Quartz`）的支持。

- `spring-expression`: Spring 表达式语言

### Aop

提供了切面编程方面的支持，由`spring-aop`、`spring-aspects`、`spring-instrument`组成

- `spring-aop`: 提供Aop（切面编程）的支持

- `spring-aspects`: 整合了`AspectJ`

### Messaging

- `spring-messaging`: Spring 4及以后新增，它提供了对消息传递体系结构和协议的支持

### Data Access/Integration

包括JDBC、ORM、OXM、JMS和Transactions模块：

- `spring-jdbc`: java jdbc的封装

- `spring-tx`: 提供事务支持

- `spring-orm`: 流行的对象关系映射`(object-relational mapping )API`提供集成层，包括`JPA`和`Hibernate`

- `spring-oxm`: 提供了一个支持`对象/ XML`映射实现的抽象层，如`JAXB`、`Castor`、`JiBX`和`XStream`

- `spring-jms`: 包含用于生产和消费消息的功能。自Spring Framework 4.1以来，它提供了与`spring-messaging`模块的集成

### Web

由`spring-web`，`spring-webmvc`和`spring-websocket`模块组成：

- `spring-web`模块提供基本的面向Web的集成功能，例如多部分文件上传功能，以及初始化一个使用了`Servlet`侦听器和面向`Web`的应用程序上下文的`IoC`容器。它还包含一个`HTTP`客户端和`Spring`的远程支持的`Web`相关部分。

- `spring-webmvc`模块（也称为`Web-Servlet`模块）包含用于`Web`应用程序的`Spring`的模型-视图-控制器(MVC)和`REST Web Services`实现。 Spring的MVC框架提供了领域模型代码和Web表单之间的清晰分离，并与Spring Framework的所有其他功能集成。

### Test

- `spring-test`: spring 测试模块