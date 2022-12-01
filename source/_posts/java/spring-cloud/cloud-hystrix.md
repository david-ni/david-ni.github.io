---
title: 熔断器
description: 微服务之熔断器
categories: 
- Java
- Spring Cloud
---

## 灾难性服务雪崩

灾难性服务雪崩是指在分布式系统中，由于个别服务不可用，导致其他多个或全部服务不可用的情况。举个例子：

![image-20210325143044113](https://user-images.githubusercontent.com/7795335/113095786-b0597f80-9226-11eb-8314-e280b46f0cc3.png)

上图中，服务E被多个其他服务依赖，出于某些原因（网络不稳定或者并发太大等）导致服务E不可访问，这会导致其他服务接连发生阻塞，最后形成全部服务全部不可用的现象。

## 如何防止服务雪崩

我们不可能保证微服务中的每个成员在任何情况下都能正常调用和返回，那如何防止应个别服务不可用而导致的连锁反应呢？一般解决方式有如下几种：

### 1. 降级

当服务不可用或请求超时，直接返回一个后备数据（比如“服务不可用，请稍后”）

![image-20210329165416189](https://user-images.githubusercontent.com/7795335/113095832-c6ffd680-9226-11eb-8fff-2a860531060b.png)

### 2. 熔断

在一个时间窗口内（比如10秒），如果请求某个服务（Service E）错误的次数大于某个阈值，那么在这个时间窗剩下时间内的请求将不会访问该服务。

![image-20210329164946123](https://user-images.githubusercontent.com/7795335/113095871-d97a1000-9226-11eb-85fc-1a73100e6e31.png)

### 3. 请求缓存

如果在一段时间内，对于相同请求优先使用缓存

<img width="504" alt="image-20210330172620701" src="https://user-images.githubusercontent.com/7795335/113095978-0fb78f80-9227-11eb-95d1-f5de00d1314b.png">

### 4. 请求合并

将多个同类型的请求合并成一个请求

![image-20210329170001889](https://user-images.githubusercontent.com/7795335/113095905-e8f95900-9226-11eb-822a-3d4fba695a36.png)

### 5. 隔离

隔离分为**信号量隔离**和**线程隔离**。**信号量隔离**检测当期正在请求服务的数量是否超过阈值，如果超过则返回

![image-20210329172019212](https://user-images.githubusercontent.com/7795335/113095944-fe6e8300-9226-11eb-8bb3-3be978a6dcdf.png)

**线程隔离**为每个服务请求客户端分配一定的线程池

![image-20210329172958934](https://user-images.githubusercontent.com/7795335/113095957-04646400-9227-11eb-9eca-698da15c7400.png)

## Hystrix

![hystrix-logo-tagline-640](https://github.com/Netflix/Hystrix/wiki/images/hystrix-logo-tagline-640.png)

Hystrix是著名流媒体公司Netflix开源的项目，意在解决分布式环境中出现的“灾难性服务雪崩”。

### Hystrix 流程图

![hystrix-command-flow-chart](https://user-images.githubusercontent.com/7795335/113096043-22ca5f80-9227-11eb-8ee1-2ab934d8223a.png)

### Hystrix 在Spring中使用

#### 添加依赖

```xml
<!-- pom -->
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

#### 常用注解

1. `@EnableHystrix`启用Hystrix支持

```java
/**
 * 使用注解 @EnableHystrix 启用Hystrix支持
 * @author David Wang <https://github.com/superTerrorist>
 */
@EnableEurekaClient
@EnableHystrix
@SpringBootApplication
public class CloudHystrixApplication{
    public static void main(String[] args){
        SpringApplication.run(CloudHystrixApplication.class,args);
    }
}
```

2. `@HystrixCommand`

### Hystrix Dashboard

### Hystrix 与 Feign的结合



## 参考

1. [Hystrix wiki](https://github.com/Netflix/Hystrix/wiki/How-it-Works)
2. [SpringCloud中Hystrix容错保护原理及配置](https://mp.weixin.qq.com/s/-s7gndjIH5p2jm8LWvKSQA)
3. [Java_SpringCloud微服务之Hystrix断路器](https://www.bilibili.com/video/BV115411w7TZ)

