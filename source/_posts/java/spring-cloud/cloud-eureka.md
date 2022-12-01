---
title: Eureka(服务的注册和发现)
description: Netflix Eureka 的基本介绍
categories: 
- Java
- Spring Cloud
---

Eureka是Netflix开发的服务发现组件，Spring Cloud将它集成在其子项目spring-cloud-netflix中，以实现Spring Cloud的服务发现功能。网上流传Eureka不再维护，我看了一下github前几天刚发布1.10.3版本，所谓的不维护应该是2.x版本，1.x还依旧活跃。

## Eureka配置

> [Eureka Instance 配置项](https://github.com/spring-cloud/spring-cloud-netflix/blob/master/spring-cloud-netflix-eureka-client/src/main/java/org/springframework/cloud/netflix/eureka/EurekaInstanceConfigBean.java)、[Eureka Client 配置项](https://github.com/spring-cloud/spring-cloud-netflix/blob/master/spring-cloud-netflix-eureka-client/src/main/java/org/springframework/cloud/netflix/eureka/EurekaClientConfigBean.java)

| 配置                                    | 默认值 | 描述                                                         |
| --------------------------------------- | ------ | ------------------------------------------------------------ |
| `eureka.instance.appname`               | -      | 应用的名称                                                   |
| `eureka.instance.instance-id`           | -      | 实例的唯一标识，用于标识同一应用（appId一样）下不同的实例    |
| `lease-renewal-interval-in-seconds`     | `30`   | 设定Eureka客户端发送心跳的间隔时间                           |
| `lease-expiration-duration-in-seconds`  | `90`   | 设定过期时间，如果超过本设定时间后还未收到心跳，Eureka服务端会剔除该shi |
| `eureka.client.healthcheck.enabled`     | `true` | 是否开启健康检查                                             |
| `eureka.client.service-url.defaultZone` | -      | Eureka默认服务地址                                           |
| `eureka.client.register-with-eureka`    | `true` | 是否将该实例注册到Eureka                                     |
| `eureka.client.fetch-registry`          | `true` | 是否从Eureka服务中获取注册表信息                             |

## Euraka踩坑笔记

### 1. Eureka 服务启动时报错：`java.lang.ArrayStoreException: sun.reflect.annotation.TypeNotPresentExceptionProxy`

> 解决方案：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-context</artifactId>
</dependency>
```

## Resource

1. [Eureka Wiki](https://github.com/Netflix/eureka/wiki)
2. https://xiaoxiami.gitbook.io/spring-cloud/fu-wu-de-zhu-ce-yu-fa-xian/fu-wu-de-zhu-ce-yu-fa-xian-eureka/eureka-xiang-jie
3. https://www.jianshu.com/p/cb7fa0aa47a8
4. https://blog.csdn.net/u012410733/article/details/112303048
5. https://www.jianshu.com/p/465e7539c3a9