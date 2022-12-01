---
title: Spring Boot
description: Spring Boot 是整合Spring技术栈的一站式脚手架，能够快创建出生产级别的Spring应用，省去繁琐的配置。
categories: 
- Java
- Spring
---

Spring Boot 是整合Spring技术栈的一站式脚手架，能够快创建出生产级别的Spring应用，省去繁琐的配置。

> 特性：

1. **自动配置**：针对常用的应用功能，Spring Boot 能自动提供相关配置
2. **起步依赖**：提供多种"starter"，简化构建配置
3. **Actuator**：提供生产级别的监控、健康检查及外部化配置
4. 内嵌`Tomcat`、`Jetty`以及`Undertow` web服务器

> 系统要求

目前最新版本的Spring Boot（2.4.0）要求至少是Java8+（最高兼容到Java15），Maven 3.3+或者Gradle 6+

### Starters（场景启动器）

Spring boot声明了多个不同场景的starter，比如开发web应用直接引入`spring-boot-starter-web`

> [官方starter](https://docs.spring.io/spring-boot/docs/2.4.1/reference/htmlsingle/#using-boot-starter)

- `spring-boot-starter-parent`: 声明了开发中常用依赖的版本号、Maven插件等

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.1</version>
</parent>
```

### Actuator

Spring Boot Actuator提供了众多的web端点，通过它们我们可以了解程序运行的情况。

> 要**启用**Actuator需要在项目中引入Actuator的起步依赖：
>
> ```xml
> <dependency>
> 	<groupId>org.springframework.boot</groupId>
>   <artifactId>spring-boot-starter-actuator</artifactId>
> </dependency>
> ```

| Http 方法 | 路径            | 描述                                                       |
| --------- | --------------- | ---------------------------------------------------------- |
| GET       | `/autoconfig`   | 自动配置报告                                               |
| GET       | `/configgroups` | 描述配置属性如何注入bean                                   |
| GET       | `/beans`        | 描述应用程序上下文中的bean，以及它们之间的关系             |
| GET       | `/dump`         | 获取线程活动快照                                           |
| GET       | `/env`          | 获取全部环境属性                                           |
| GET       | `/env/{name}`   | 根据名称获取特定的环境属性                                 |
| GET       | `/health`       | 报告应用程序的健康指标                                     |
| GET       | `/info`         | 获取应用程序的定制信息                                     |
| GET       | `/mappings`     | 获取全部URI路径，以及它们和控制器的映射关系                |
| GET       | `/metrics`      | 报告各种度量信息，比如内存用量等                           |
| POST      | `/shutdown`     | 关闭应用程序，要求`endpoints.shutdown.enabled`设置为`true` |
| GET       | `/trace`        | 提供基本的HTTP请求跟踪信息                                 |

### Spring Boot Web

#### 静态资源

默认情况下，只要把静态资源放在类路径下的以下几个目录：
`/static` (or `/public` or `/resources` or `/META-INF/resources`

通过访问 `http://xxxxx/静态资源名称`

```properties
## 修改静态资源访问路径前缀
spring.mvc.static-path-pattern = /resources/**

## 修改静态资源存放的根路径
spring.web.resources.static-locations = /
```

#### 最佳实践

1. **使用Lombok** 

> [Lombok官方](https://projectlombok.org/features/all)

2. **Spring DevTool**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

> 快捷键 mac: `command+fn+F9`

- 默认配置

在开发环境，spring-boot-devtools会注入一些默认的配置，具体看移步[DevToolsPropertyDefaultsPostProcessor.java](https://github.com/spring-projects/spring-boot/blob/v2.4.1/spring-boot-project/spring-boot-devtools/src/main/java/org/springframework/boot/devtools/env/DevToolsPropertyDefaultsPostProcessor.java)

- 自动重启

> https://docs.spring.io/spring-boot/docs/2.4.1/reference/htmlsingle/#using-boot-devtools-restart

如果`classpath`中的文件发生变化，应用自动重启

- LiveReload

当资源变化时，浏览器自动刷新

> To trigger LiveReload when a file changes, Automatic Restart must be enabled.

- Global Settings
- Remote Applications

3. **自定义类绑定的配置提示**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

## 附录-Spring 常用注解

- **@SpringBootApplication**

`@SpringBootApplication`用于入口应用类，相当于使用`@EnableAutoConfiguration`、`@ComponentScan`以及`@Configuration`这三个注解。


```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// same as @Configuration @EnableAutoConfiguration @ComponentScan
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

- **@EnableAutoConfiguration**

是否自动加载Spring所需的配置

> [Spring自动装配原理](https://www.bilibili.com/video/BV19K4y1L7MT?p=14)

- **@Configuration**

指示一个类声明一个或多个`Bean`方法，并注册到Spring 容器中

```java
@Configuration
public class MyConfiguration {
    @Bean
    public MyBean myBean() {
		return new MyBean();
	}
}
```

- **@ComponentScan**

指定要扫描的包，所有被`@Component`、`@Service`、`@Repository`、`@Controller`等注解的类都将自动注册到Spring容器中

```java
@SpringBootConfiguration
@ComponentScan(basePackages = "st.java.springboot")
@EnableAutoConfiguration
public class Application{
    public static void main(String[] args){
       ConfigurableApplicationContext context = SpringApplication.run(Application.class,args);
    }
}
```

- **@Import**

添加指定的类到spring容器中

```java
@Configuration
@Import({ExampleBean.class})
public class AppConfig{
    // ....
}
```
不同于`@ComponentScan`，`@Import`可以指定一个或多个类，这有助于将第三方依赖（非springs实现）添加到容器中。

- **@Conditional 条件装配**
- @ImportResource 导入配置文件
- @ConfigurationProperties
- @EnableConfigurationProperties

## 文档

- [Spring Boot Release Notes](https://github.com/spring-projects/spring-boot/wiki#release-notes)
- [Spring Boot 官方文档](https://docs.spring.io/spring-boot/docs/2.4.1/reference/htmlsingle/)
- [SpringBoot2零基础入门](https://www.bilibili.com/video/BV19K4y1L7MT)