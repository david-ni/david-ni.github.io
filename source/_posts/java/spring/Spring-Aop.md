---
title: Spring Aop
description: Spring Aop 的介绍
categories: 
- Java
- Spring
---

AOP（Aspect Oriented Programming）即切面编程。AOP能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任，例如事务处理、日志管理、权限控制、异常处理等，封装起来，便于减少系统重复的代码，降低模块之间的耦合度。

简单总结，AOP能够在不该动或少该动原有代码的基础上，实现功能增强。

本文按以下几个部分开展：

- AOP的底层实现

- AOP中的一些术语
- 在Spring中的使用AOP

### AOP的底层实现

```
// todo
```

### AOP的一些术语

磨刀不误砍材功，在介绍AOP术语之前，我们先准备一个类

```java
package com.spring.aop;
public class UserDaoImp implements UserDao{
    @Override
    public User find(){
        return new User();
    };
    
    @Override
    public void save(User user){};
   
    @Override
    public void update(){};
  
    @Override
    public void remove(){};
}
```

#### 连接点（JoinPoint）

表示“可以在哪里做”，它用来定义程序中有哪些地方可以使用AOP加入额外的逻辑。比如在`UserDaoImp`类中，`find`、`save`、`update`、`remove` 这些方法都可以被增强，这都可以被称之为连接点。

#### 切入点（Pointcut）

表示“实际在哪里做”，连接点可以表示岗位候选人，切入点则是实际邀约的人。也就是具体拦截的点，通过创建切入点，我们可以精确地控制程序中什么组件接到什么通知。

#### 通知/增强（Advice）

表示“怎么做或者说做什么”，即增强的具体实现，比如我们要对用户做权限判断，那么这个权限判断的实现就是通知。通知有多种类型：

1. 前置通知（Before advice）
2. 后置通知（After returning advice）
3. 环绕通知（Around advice）
4. 异常抛出通知（After throwing advice）
5. 最终通知 （After (finally) advice）

#### 切面

将通知应用到具体切入点的这个动作就是切面

### 在Spring中的使用AOP

#### 1. 开启对AspectJ的支持

#### 2. 声明切面

#### 3. 声明切入点

#### 4. 声明通知

## 参考

- [Aspect Oriented Programming with Spring](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-introduction-defn)