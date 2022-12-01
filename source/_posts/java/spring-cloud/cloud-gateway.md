---
title: 微服务网关
description: 微服务之网关
categories: 
- Java
- Spring Cloud
---

微服务网关是整个微服务API请求的入口，可以实现Api过滤、用户鉴权、解决跨域、限流、日志拦截等。

![image](/images/spring-cloud/image-20210402172318762.png)

市面上常用的网关有以下几种：

- Nginx + Lunar
- Netflix Zuul
- Spring Cloud  Gateway

## Zuul

### 依赖

> 本文使用的版本：Spring Boot **2.3.8.RELEASE**、Spring Cloud **Hoxton.SR10**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

### 启用

```java
@SpringBootApplication
@EnableZuulProxy
public class RouteApplication{
    public static void main(String[] args){
        SpringApplication.run(RouteApplication.class,args);
    }
}
```

#### `@EnableZuulProxy`和`@EnableZuulServer`的区别

`@EnableZuulServer`和`@EnableZuulProxy` 都内置了一些的过滤器，`@EnableZuulProxy`可以简单理解为`@EnableZuulServer`的增强版，当Zuul与Eureka、Ribbon等组件配合使用时，我们使用`@EnableZuulProxy`

##### `@EnableZuulServer`内置过滤器

| 过滤器名称               | 过滤器类型   | 描述                                                         |
| ------------------------ | ------------ | ------------------------------------------------------------ |
| `ServletDetectionFilter` | Pre Filter   | 该过滤器用于检查请求是否通过Spring Dispatcher。              |
| `FormBodyWrapperFilter`  | Pre Filter   | 解析表单数据，并为请求重新编码                               |
| `DebugFilter`            | Pre Filter   | 顾名思义，调试用的过滤器，可以通过`zuul.debug.request=true`，或在请求时，加上`debug=true`的参数，例如`$ZUUL_HOST:ZUUL_PORT/path?debug=true`开启该过滤器。这样，该过滤器就会把`RequestContext.setDebugRouting()`、`RequestContext.setDebugRequest()`设为`true` |
| `SendForwardFilter`      | Route Filter | 该过滤器使用Servlet RequestDispatcher转发请求                |
| `SendResponseFilter`     | Post Filter  | 将Zuul所代理的微服务的响应写入当前响应                       |
| `SendErrorFilter`        | Error Filter | 如果`RequestContext.getThrowable()`不为null，那么默认就会转发到`/error`，也可以设置`error.path`属性修改默认的转发路径 |

##### `@EnableZuulProxy`内置过滤器

除了继承来自`@EnableZuulServer`过滤器外，还拥有以下过滤器：

| 过滤器名称                | 过滤器类型   | 描述                                                         |
| ------------------------- | ------------ | ------------------------------------------------------------ |
| `PreDecorationFilter`     | Pre Filter   | 该过滤器根据提供的`RouteLocator`确定路由到的地址，以及怎样去路由。该路由器也可为后端请求设置各种代理相关的header |
| `RibbonRoutingFilter`     | Route Filter | 该过滤器使用Ribbon，Hystrix和可插拔的HTTP客户端发送请求      |
| `SimpleHostRoutingFilter` | Route Filter | 该过滤器通过Apache HttpClient向指定的URL发送请求             |

### 配置

#### 1. 设置路由前缀

```yaml
zuul:
  prefix: /api
  strip-prefix: true
```

这里需要重点说一下`strip-prefix`这个属性，`strip-prefix`表示当请求被转发到具体微服务是`prefix` 是否会被剔除。

比如我们发起请求`http://localhost:8080/api/cloud-demo/hello`

如果`strip-prefix`设成`true`, 那么`cloud-route`接收到的请求就是`/hello`

如果`strip-prefix`设成false, 那么`cloud-route`接收到的请求就变成`/api/hello`，此时`/api/`这个前缀没有被剔除

#### 2. 设置路由规则

```yaml
zuul:
  routes:
    cloud-demo:
    	# serviceId 是注册到Eureka的应用名称
      serviceId: cloud-demo
      path: /cloud-demo/**
      # url: http://localhost:8081
```

这里的`serviceId`指的的注册到Eureka的应用名称，当网关接收到的请求地址与`path`中规中匹配，那么请求将被转发到`serviceId`对应的服务。

> 通配符规则

| 通配符 | 含义                                 | 举例           | 匹配                                     |
| ------ | ------------------------------------ | -------------- | ---------------------------------------- |
| ?      | 匹配任意单个字符                     | /cloud-demo/?  | /cloud-demo/1                            |
| *      | 匹配任意数量的字符但不包括子路由     | /cloud-demo/*  | /cloud-demo/hello                        |
| **     | 匹配任意数量的字符并包括所有下级路由 | /cloud-demo/** | /cloud-demo/hello、/cloud-demo/hello/say |

#### 3. 路由排除

```yaml
zuul:
  ignored-patterns: /**/say/** # url地址排除 排除所有包含/say/的路径
  ignored-services: cloud-demo # 服务名称排除，多个服务用逗号分隔，'*'表示排除所有
```

#### 4. 过滤请求头

```yaml
zuul: 
	sensitive-headers: Cookie,Set-Cookie,Authorization
	cloud-demo:
    	sensitive-headers: Cookie,Set-Cookie,Authorization
```

`sensitive-headers` 用于过滤特定的请求头，可以全局使用也可以应用在特定路由（比如`cloud-demo`）



### 自定义`Filter`



## 参考

1. [How is Spring Cloud Gateway different from Zuul?](https://stackoverflow.com/questions/47092048/how-is-spring-cloud-gateway-different-from-zuul)
2. [Zuul Wiki](https://github.com/Netflix/zuul/wiki/Getting-Started)

