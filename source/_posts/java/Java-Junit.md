---
title: Java 单元测试之Junit
description: 单元测试是代码健壮的有效保障，是任何一个合格的开发人员都必须掌握的。JUnit作为Java开发中使用最广泛的测试框架，我们理当有所了解
categories: 
- Java
---

> 本文整理自：[
> SpringBoot2核心技术与响应式编程](https://www.yuque.com/atguigu/springboot)

单元测试是代码健壮的有效保障，是任何一个合格的开发人员都必须掌握的。JUnit作为Java开发中使用最广泛的测试框架，我们理当有所了解。

JUnit5 是JUnit的最新版本，与先前版本不同的是：JUnit5被分成三个模块：

> JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage

- **JUnit Platform**：Junit Platform是在JVM上启动测试框架的基础，不仅支持Junit自身的测试引擎，其他测试引擎也都可以接入
- **JUnit Jupiter**：JUnit Jupiter提供了JUnit5的新的编程模型，是JUnit5新特性的核心。内部包含了一个测试引擎，用于在Junit Platform上运行
- **JUnit Vintage**：由于JUint已经发展多年，为了照顾老的项目，JUnit Vintage提供了兼容JUnit4.x,Junit3.x的测试引擎。

> [JUnit5官方文档](https://junit.org/junit5/docs/current/user-guide/#overview)

## 依赖

```xml
<!--单纯使用junit-->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.7.0</version>
    <scope>test</scope>
</dependency>
```

```xml
<!--如果使用spring-boot直接引入starter包--->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

## JUnit 常用注解

> [JUNIT支持的所有注解](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations)

- `@Test`: 标记测试方法。

```java
@Test
public void isTestFun(){
  //.....
}
```

- `@ParameterizedTest`：参数化测试

参数化测试是JUnit5很重要的一个新特性，它使得用不同的参数多次运行测试成为了可能，也为我们的单元测试带来许多便利。

利用`@ValueSource`等注解，指定入参，我们将可以使用不同的参数进行多次单元测试，而不需要每新增一个参数就新增一个单元测试，省去了很多冗余代码。

`@ValueSource`: 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型

`@NullSource`: 表示为参数化测试提供一个null的入参

`@EnumSource`: 表示为参数化测试提供一个枚举入参

`@CsvFileSource`：表示读取指定CSV文件内容作为参数化测试入参

`@MethodSource`：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)

```java
@ParameterizedTest
@ValueSource(strings = {"one", "two", "three"})
@DisplayName("参数化测试1")
public void parameterizedTest1(String string) {
    System.out.println(string);
    Assertions.assertTrue(StringUtils.isNotBlank(string));
}


@ParameterizedTest
@MethodSource("method")    //指定方法名
@DisplayName("方法来源参数")
public void testWithExplicitLocalMethodSource(String name) {
    System.out.println(name);
    Assertions.assertNotNull(name);
}

static Stream<String> method() {
    return Stream.of("apple", "banana");
}
```

- `@RepeatedTest`：表示方法可重复执行

- `@DisplayName`: 为测试类或者测试方法设置展示名称
- `@BeforeEach`：表示在每个单元测试之前执行
- `@AfterEach`: 表示在每个单元测试之后执行
- `@BeforeAll `: 表示在所有单元测试之前执行
- `@AfterAll `: 表示在所有单元测试之后执行
- `@Disabled` : 表示测试类或测试方法不执行，类似于JUnit4中的@Ignore
- `@Timeout` : 表示测试方法运行如果超过了指定时间将会返回错误



## JUnit中的断言

> 需要导入 `import static org.junit.jupiter.api.Assertions.*;`

| 方法              | 说明                                 |
| ----------------- | ------------------------------------ |
| assertEquals      | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals   | 判断两个对象或两个原始类型是否不相等 |
| assertSame        | 判断两个对象引用是否指向同一个对象   |
| assertNotSame     | 判断两个对象引用是否指向不同的对象   |
| assertTrue        | 判断给定的布尔值是否为 true          |
| assertFalse       | 判断给定的布尔值是否为 false         |
| assertNull        | 判断给定的对象引用是否为 null        |
| assertNotNull     | 判断给定的对象引用是否不为 null      |
| assertArrayEquals | 判断两个对象或原始类型的数组是否相等 |
| assertAll         | 组合断言                             |
| assertThrows      | 异常断言                             |
| assertTimeout     | 超时断言                             |

```java
@Test
@DisplayName("first test")
public void firstTest(){
   // 判断两个对象或两个原始类型是否相等
   assertEquals(1,2,"it's not equal!");
        
   Object o1 = new Object();
   Object o2 = new Object();
   // 判断两个对象引用是否指向同一个对象
   assertSame(o1,o2);

   // 判断给定的布尔值是否为 true
   assertTrue(1==1);
  
   // 数组断言
   assertArrayEquals(new int[]{1, 2}, new int[] {1, 2});
  
   // 组合断言
   assertAll("Math",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(1 > 0)
   );
  
   // 超时断言
   Assertions.assertTimeout(Duration.ofMillis(1000), () -> Thread.sleep(500));
}
```