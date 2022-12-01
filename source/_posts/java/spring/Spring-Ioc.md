---
title: Spring IOC
description: IOC（控制反转）也可称为依赖注入（DI），用于解耦对象之间复杂的依赖关系。
categories: 
- Java
- Spring
---

> [官方文档](https://docs.spring.io/spring-framework/docs/5.2.16.RELEASE/spring-framework-reference/core.html#beans)

IOC（控制反转）也可称为依赖注入（DI），用于解耦对象之间复杂的依赖关系。

![spring-ioc-1](https://user-images.githubusercontent.com/7795335/102582360-621fe500-413d-11eb-9a4b-8b516829a158.jpg)

如上图，对象之间存在错综复杂的依赖关系，牵一发而动全身。

![spring-ioc-2](https://user-images.githubusercontent.com/7795335/102582371-68ae5c80-413d-11eb-8d8d-194f5f47c839.jpg)

而IOC就是引进了中间位置的“第三方”，将依赖的控制权收归于一方，让各个对象依赖“第三方”。

> [IOC的简单实现](https://github.com/superTerrorist/My-Blog/tree/master/Code/Java/src/main/java/st/blog/java/ioc)

Spring 提供了两种容器类型：[BeanFactory](https://docs.spring.io/spring-framework/docs/5.3.2/javadoc-api/org/springframework/beans/factory/BeanFactory.html) 和 [ApplicationContext](https://docs.spring.io/spring-framework/docs/5.3.2/javadoc-api/org/springframework/context/ApplicationContext.html)

- **BeanFactory**: 基础类型IoC容器，提供完整的IoC服务支持。如果没有特殊指定，默认采用延迟初始化策略（lazy-load）

- **ApplicationContext**: 在`BeanFactory`的基础上构建，是相对比较高
级的容器实现，除了拥有`BeanFactory`的所有支持，`ApplicationContext`还提供了其他高级特性，比如事件发布、国际化信息支持等

### Bean

IOC的实质是通过容器把依赖集中管理以此解决对象间相互依赖导致的问题。而IOC容器管理着一个或多个`bean`。

### Bean 的配置

#### 标识符（[Naming beans](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-beanname)）

```xml
<bean id="beanIdentifier" name="beanAlias" class="com.demo.spring.BeanBaseImp"></bean>
```

每个bean都有一个或多个标示符，这些标示符在容器中必须是唯一的。

我们可以使用id/name来指定bean的标识符，具体可以参考[id和name的区别](https://blog.csdn.net/aitangyong/article/details/50629525)

#### 实例化 （[Instantiating beans](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class)）

bean的实例化，有三种方式：

- 无参构造函数

```xml
<bean id="exampleBean" class="examples.ExampleBean"/>
```

- 静态方法实例化

```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```

```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```

- 工厂方法实例化

```xml
<!--先实例化bean-->
<bean id="instanceFactory"
    class="com.spring.bean.instance.InstanceFactoy">
</bean>
<!--再调用实例化方法-->
<bean id="instanceFactoryConstruct"
    factory-bean="instanceFactory"
    factory-method="getInstance"
></bean>
```

```java
package com.spring.bean.instance;

public class InstanceFactory {
    public InstanceFactory getInstance(){
        System.out.println("Instance By Factory!");
        return new InstanceFactory();
    }
}
```

#### Scopes （[Bean scopes](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes)）

```xml
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```

| Scopes                                                       | Desc                                         |
| ------------------------------------------------------------ | -------------------------------------------- |
| [singleton](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-singleton) | 对于一个Spring Ioc容器中有且仅有一个bean实例 |
| [prototype](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype) | 每次调用`getBean`都会产生一个新的实例        |
| [request](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-request) |                                              |
| [session](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-session) |                                              |
| [application](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-application) |                                              |
| [websocket](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#websocket-stomp-websocket-scope) |                                              |

> 注：
> `request`, `session`, `globalSession`, `application`和 `websocket` 只适用于web相关的上下文中比如`XmlWebApplicationContext`，如果在其他上下文中使用（比如：`ClassPathXmlApplicationContext`），会报`IllegalStateException`的错误

### 使用注解配置Bean

#### `@Configuration`

用于Spring中bean的配置，Spring 3.0开始支持。

文档参考：

- [Java-based Container Configuration](https://docs.spring.io/spring/docs/4.3.25.RELEASE/spring-framework-reference/htmlsingle/#beans-java)

在注解出现之前，Spring Bean的配置 使用的是XML的形式：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">   
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->
</beans>
```
这里`id`是唯一标识，`class`是Bean对应的类。

随后我们需要加载配置：

```java 
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "....xml");

// get Bean
context.getBean("myBean");
```
这样的略显繁琐，后期维护也很麻烦。`@Configuration`的出现解决了这个问题。

```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

这等价于：

```
<beans>
    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
</beans>
```
而后注册配置：

```java
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.register(AppConfig.class);
MyService myService = ctx.getBean(MyService.class);
myService.doStuff();
```

#### `@Component`

主要用于将一个Java类注入到Spring框架中，它相当于XML配置文件中的`<bean id=”xxx” class=”xxx”/>`

除了@Component注解，Spring容器提供了3个功能和`@Component`注解等同。它们分别是用于对Dao，Service及Web层的Controller进行注解:

- `@Repository`：用于对Dao实现类注解
- `@Service`：用于对Service实现类注解
- `@Controller`：用于对Controller实现类注解

**`@Bean` 和 `@Component`的区别**

1. `@Bean`与`@Configuration`配合使用，是对方法的注解
2. `@Component`是对类层面的注解

#### `@Autowired`

> [Guide to Spring @Autowired](https://www.baeldung.com/spring-autowire)

根据类型判断要注入的类

```java
// User.java
package com.spring.bean.annotation;

import org.springframework.stereotype.Service;

@Service
public class User {
    public String getName(){
        return "superTerrorist";
    }
}
```

```java
package com.spring.bean.annotation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component(value = "app")
public class App {
    // 根据类型判断要注入的类
    @Autowired(required = true)
    private User user;

    public String getUserName(){
        return this.user.getName();
    }
}
```

#### `@Qualifier`

根据名称注入

```java
// User.java
package com.spring.bean.annotation;

import org.springframework.stereotype.Service;

@Service(value = "user")
public class User {
    public String getName(){
        return "superTerrorist";
    }
}
```

```java
package com.spring.bean.annotation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component(value = "app")
public class App {
    // 根据名称判断
    @Qualifier("user")
    private User user;

    public String getUserName(){
        return this.user.getName();
    }
}
```

#### `@Resource`

根据名称和类型判断，相当于`@Qualifier`和`@Autowired`的结合

```java
package com.spring.bean.annotation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component(value = "app")
public class App {
    
    @Autowired()
    @Qualifier("user")
    private User user;

    public String getUserName(){
        return this.user.getName();
    }
}
```

### Bean的生命周期

![Spring-bean-life-cycle](https://user-images.githubusercontent.com/7795335/102587006-19b8f500-4146-11eb-8bcd-697fdb7c4d7c.jpg)

可参考《Spring揭秘》4.4.3 了解bean的一生