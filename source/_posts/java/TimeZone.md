---
title: 记一次时区引发的BUG
description: 前几日测试同事反馈了一个奇怪的bug
categories: 
- Java
---

## 问题描述

前几日测试同事反馈了一个奇怪的bug，**前端获取的时间与数据库的时间相差了8小时**。

## 排查

8这个数字条件反射般的让人容易想起**时区**，按这个思路排查应该是抓住了线头：

1. 排查数据库时区

   使用`show variables like "%time_zone%"`查询`MySql`的时区配置，发现妥妥的北京时间（`CST`）

2. 排查宿主机和Docker时区

   在使用`date -R`看了一下宿主机和Docker的时间，都是东八区` +0800`

环境时区没问题，难道是java读取的不是环境默认时区？

3. 获取java运行时的默认时区

   ```java
   public class TimeZoneTest{
       public static void main(String[] args) {
           System.out.println("timezone: " + TimeZone.getDefault().getID());
       }
   }
   ```

打印出来的的时区为`GMT`，这算找到了初步的线索，但为什么java拿到的时区与系统不一致呢？一开始以为是启动时被指定了时区，认真看了好几次docker执行脚本，没有发现问题。



最后还是在某度上找到了答案: [docker容器 java 默认读取系统时区问题](https://blog.csdn.net/SoberChina/article/details/84849860)，java获取时区的流程可以概括为：

1. 获取`user.timezone`变量的时区，如果没有到2
2. 返回系统默认的时区

而在Linux系统上，大概过程为以下几步：

1. 先找`TZ`变量，没有，到2
2. 读`/etc/timezone`，没有到3
3. 比较`/etc/localtime`文件与`/usr/share/zoneinfo`目录下所有时区文件，如果有一致的，就为该时区，如果没有，到4
4. 默认为标准`GMT`

大致知道了这个过程，然后开始分析`docker container` ,进入容器查看相应的目录结构，`echo $TZ` 无输出 到2，未找到`timezone`文件 转3 有`localtime` 文件(再启动容器的时候localtime是挂载进去的) 然后再去查看`ls /usr/share/zoneinfo` 未找到该目录，最终发现alpine基础镜像并没有时区文件

## 解决

1. docker 挂载时区文件
2. 配置`TZ`环境变量
3. Dockerfile 添加`RUN echo "Asia/Shanghai" > /etc/timezone` 配置

## 附录

### GMT 和 UTC

`GMT`和`UTC`都是世界标准时，但`GMT`是前世界标准时，`UTC`是现世界标准时。

`UTC` 比 `GMT`更精准，以原子时计时，适应现代社会的精确计时。

`UTC+8`表示北京时间



### 时间戳

时间戳指的就是**Unix时间戳**(Unix timestamp)。它也被称为Unix时间(Unix time)，是从1970年1月1日（UTC/GMT的午夜）开始所经过的秒数。



**时间戳与时区无关的**，也就是同一时间不同时区时间戳是一样的，**这里的同一时间指的是同一UTC时间**，比如`UTC+8`的`2021-08-04 18:57:33`与`UTC+7`的`2021-08-04 15:57:33`的时间戳都是`1628074653402`，因为这两个的`UTC`时间都是`2021-08-04 10:57:33`

