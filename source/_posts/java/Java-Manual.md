---
title: Java Manual
description: 初学Java时整理的笔记
categories: 
- Java
---

## 名词解释

### Java POJO 和 Java Bean

`Java POJO`：简单Java类，这个类没有实现/继承任何特殊的java接口或者类，不遵循任何主要java模型，约定或者框架的java对象以及不应该有注解。

`Java Bean`:  是可序列化的，实现了serializable接口；具有一个无参构造器； 属性具有getter和setter方法；

> [Difference between DTO, VO, POJO, JavaBeans?](https://stackoverflow.com/questions/1612334/difference-between-dto-vo-pojo-javabeans)

### Ant vs Maven vs Gradle

[Ant vs Maven vs Gradle](https://www.baeldung.com/ant-maven-gradle)

##  Java 数据类型

###  基本数据类型

Java是一种强类型的语言。Java数据类型可分为基本类型和引用类型。

| 类型           | 大小（字节） | 默认值   | 最小值           | 最大值                           |
| -------------- | ------------ | -------- | ---------------- | -------------------------------- |
| byte(位)       | 1            | 0        | -128             | 127                              |
| short(短整数)  | 2            | 0        | -2<sup>^15</sup> | 2<sup>^15</sup> - 1              |
| int(整数)      | 4            | 0        | -2<sup>^31</sup> | 2<sup>^31</sup> - 1 （超过20亿） |
| long(长整数)   | 8            | 0L       | -2<sup>^63</sup> | 2<sup>^63</sup> - 1              |
| float(单精度)  | 4            | O.0F     | 1.4e-45          | 3.4e+38（有效位数6~7位）         |
| double(双精度) | 8            | 0.0D     | 4.9e-324         | 1.798e+308（有效位数15位）       |
| char(字符)     | 2            | '\u0000' | '\u0000'         | '\uFFFF'                         |
| boolean        | 1            | false    | false(0)         | true(1)                          |

> **注释：**
> - Java没有任何无符号形式的int、long、short、byte类型
> - 没有F/f的浮点数值（如：3.14）默认为double类型
> - char类型的字面量值要用单引号括起来，如：char charactar = 'a';
> - 整型值和布尔值之间不能相互转换

### 包装类型

Java 的每个基本类型都对应了一个包装类型

| 基本类型 | 包装类型  |
| -------- | --------- |
| byte     | Byte      |
| short    | Short     |
| int      | Integer   |
| long     | Long      |
| float    | Float     |
| double   | Double    |
| char     | Character |
| boolean  | Boolean   |

#### 基本类型和包装类型的区别

1. 初始值不同

包装类型初始值为`null`，而基本类型的初始值视类型而定。

2. 包装类型可用于泛型，而基本类型不可以

比如集合类只能使用包装类型

3. 基本类型比包装类型更高效

基本类型存储在栈中，而包装类型存储在堆中，显然基本类型存储相对简单，运算效率更高。

4. 两个包装类型的值可以相同，但却不相等

包装类型之间比较的是内存地址，而不是值，不能简单的使用`==`进行判断，使用`equal`更为靠谱。

```java
Integer wrapper1 = new Integer(1);
Integer wrapper2 = new Integer(1);
System.out.println(wrapper1 == wrapper2); // false
```

#### 装箱和拆箱

装箱和拆箱说白了就是基本类型和包装类型两者之间的转换。把基本类型转换成包装类型的过程叫做装箱（boxing）

```java
Integer.valueOf(1); // 装箱
```

反之，把包装类型转换成基本类型的过程叫做拆箱（unboxing）

```java
Integer wrapper = new Integer(1);
int base = wrapper.intValue(); // 拆箱
```

在 Java SE5 之前，开发人员要手动进行拆装箱，Java SE5 为了减少开发人员的工作，提供了自动装箱与自动拆箱的功能

```java
Integer wrapper  = 10;  // 自动装箱
int base = wrapper;   // 自动拆箱
```

自动拆装箱是在编译过程中完成的，编译器会自动调用`Integer.valueOf()`和`Integer.intValue()`来实现装箱和拆箱。

#### 缓存池

上面有提到"包装类型之间比较的是内存地址，而不是值，不能简单的使用`==`进行判断"，但是当我们使用`valueOf`来包装对象时，情况有所不同：

```java
Integer wrapper1 = Integer.valueOf(1);
Integer wrapper2 = Integer.valueOf(1);
System.out.println(wrapper1 == wrapper2); // true
```

可以看到使用`==`比较两个对象时，其结果为`true`，违背了`==`比较的是对象地址而不是值的预期。为什么会这样的差异，我们可以从源码中探得端倪

```java
public static Integer valueOf(int i){
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

从源码中我们可以知道，当数值在一定范围内，会从缓存中取值，而不是实例化。这种机制不止存在于`Interger`中，其他包装对象也有同样的处理。

| 包装对象  | 缓存池范围     |
| --------- | -------------- |
| Byte      | `-128 ~ 127`   |
| Short     | `-128 ~ 127`   |
| Integer   | `-128 ~ 127`   |
| Long      | `-128 ~ 127`   |
| Float     | 无             |
| Double    | 无             |
| Character | `0 ~ 127`      |
| Boolean   | `true`/`false` |

## Java 字符串

从概念上讲，Java字符串就是Unicode字符序列。比如，字符串"Java"，由'J'、'a'、'v'、'a'四个字符组成。Java没有内置的字符串类型，而是在标准Java类库中提供一个预定义类：`String`。

### 创建

创建字符串常用的方法有:

- 通过构造函数

```java
// 方法一
String str = new String("example");

// 通过字符数组
char chars[] = {'e','x','a','m','p','l','e'};
String str = new String(chars);
```

- 通过字面量

```java
String str = "example";
```

> 通过字面量创建的字符串会存放在常量池中，而通过构造函数的会存放在堆中。

### 拼接

在Java中，使用`+`号连接字符串：

```java
String str1 = "str1";
String str2 = "str2";
System.out.println(str1 + str2);
```
任何一个对象都可以被转换成字符串。

### 字符串不可变

在Java中，字符串对象是不可变的，这里的不变性是指：对字符串的增删会创建一个新的字符串对象，而不是在原有对象上修改。

如果需要对字符串进行频繁的修改，为了提高效率可以使用`StringBuilder`类

### 字符串的比较

由于字符串不是基础类型，对其的比较不能使用`==`，而是`equals`

```
String str1 = "str1";
String str2 = "str2";

str1.equals(str2); // true
str1 == str2; // false
```
`==`比较的是对象引用地址是否相同，`equals`比较的是字符串字面值是否相同。

### 字符串的长度

> [一个Java字符串中到底有多少个字符?](https://colobu.com/2019/01/04/how-many-charactors-in-a-java-string/)

在Java中，可以使用`length`获取字符串的长度，不过这里需要注意的是，这里的长度表示的是代码单元的数量，一个代码单元即一个字符（char）的长度（2个字节）

```
String str2 = "abc"; 
String str3 = "😨";

str2.length(); // 3
str3.length(); // 2

str3.getBytes().length; // 6字节
str3.getBytes().length; // 4字节
```

### 常量池

[String — 字符串常量池与intern](https://juejin.im/post/5c160420518825235a05301e)

## Java 数组

数组是一种数据结构，用来存储同一类型值的集合。

### 创建

对于数组的创建有两种方式：

- 构造函数：` int a[] = new int[100];`
- 字面量：`int a[] = {1,2,3,4};`

**数组一旦创建，就不能改变其大小。**

### 初始化

创建一个数组时，如果没有对其赋值，那么所有元素都会被初始化。

| 数组类型 | 初始化值 |
| -------- | -------- |
| 数字     | 0        |
| 布尔值   | false    |
| 对象     | null     |

## Java 类

```java
[public] [final] [abstract] class ClassName{
    [访问修饰符] [static] [final] [类型] 域;
    [访问修饰符] [static] [final] [类型] 方法;
}
```
在一个源文件中，**只有一个公有类**，但是可以有**多个任意数量的非公有类**。

### 构造函数

- 构造函数与类同名
- 每个类可以有一个或多个构造函数
- 构造函数有0个、1个或多个参数
- 构造函数没有返回值，无需指定`void`

```java
class Employee{
    public Employee(){}
}
```

- 构造函数总是伴随着new操作一起调用
- 不要在构造函数中定义与实例域重名的局部变量
- 当且仅当类没有提供任何构造方法的时候，系统会提供一个默认无参数构造函数

```java
class Employee{
    private String name = "";
    public Employee(String n){
        String name = n; // Error
    }
}
```
### 访问修饰符

在Java中有四中访问修饰符 `private`、`public`、`protect`和`default`，不同修饰符可见性如下表：

| /       | 类内部  | 本包    | 子类    | 外部包  |
| ------- | ------- | ------- | ------- | ------- |
| public  | &check; | &check; | &check; | &check; |
| protect | &check; | &check; | &check; |         |
| default | &check; | &check; | &check; |         |
| private | &check; |         |         |         |

### 继承

```java
class ChildClass extends ParentClass{}
```
- 子类有且仅有一个父类，一个父类可以有多个子类
- 如果子类没有显式的调用父类构造器，则会自动的调用父类默认构造器。

```java
class Animal{
  public Animal(){
    System.out.println("I am animal");
  }
}

class Cat extends Animal{}

class Main {
  public static void main(String[] args) {
    Cat cat = new Cat(); // I am animal
  }
}
```
如果父类没有不带参数的构造器，并且子类没有显式调用，Java编译器将会报告错误。

```java
class Animal{
  public Animal(String name){
    System.out.println("I am animal" + name);
  }
}

class Cat extends Animal{}

class Main {
  public static void main(String[] args) {
    Cat cat = new Cat(); // 报错
  }
}
```

#### 方法的重载和重写

##### 重载（Overload）

如果多个方法有相同的名称、不同的参数，便产生重载。

- 同一个类中
- 方法名相同，参数列表不同（参数个数、类型、顺序）
- 方法返回值、访问修饰符任意
- 与参数名无关

```java
# String类中有4个indexOf方法

- indexOf(int)
- indexOf(int,int)
- indexOf(String)
- indexOf(String,int)
```

##### 重写（Overwrite）

- 有继承关系的子类
- 参数名相同、参数列表相同、返回值兼容
- 访问修饰符不能低于父级修饰符，==如果超类是`public`，那么子类方法一定要声明为public==
- 与参数名无关

#### 向上转型和向下转型

```java
class Animal{ //... }
class Cat extends Animal{ //... }
```
这里声明两个类，`Animal`和`Cat`，`Animal`是`Cat`的父类。

- **向上转型**

即子类对象赋值给父类引用：

```
Animal animal = new Cat();
```
此时，无需强制转换

- **向下转型**

子类对象的父类引用赋给子类引用

```
Animal animal = new Cat();
Cat cat = (Cat)animal;
```
注意：这里的`animal`本质上还是`Cat`对象，下面的做法是错误的：

```
Animal animal = new Animal();
Cat cat = (Cat)animal; // error
```

### 抽象类

```java
[public] abstract class AbstractClass{
    private String name;
    public String getName(){};
    public abstract String getDesc();
}
```
- 包含一个或多个抽象方法的类，必须声明为抽象类
- 除了抽象方法外，抽象类可以包含具体的数据域和方法
- 类即使不包含抽象方法，也可以被声明成抽象类
- 抽象类不能被实例化

## Object类

在Java中，Object类是所有类的超类，每个类都是由它扩展而来的。可以使用Object类型的变量引用任何类型的对象。

### `equal` 方法

该方法用于检测一个对象是否等于另一个对象。在Object类中，这个方法的默认实现是判断两个对象是否有相同的引用。

Java语言规范要求equals方法具有下面的特性:

- 自反性：对于任何非空引用x，`x.equals(x)`应该返回true.

- 对称性：对于任何引用x和y，当且仅当`y.equals(x)`返回`true`，`x.equals(y)`也应该返回true

- 传递性：对于任何引用x、y和z，如果`x.equals(y)`返回`true`, `y.equals(z)`返回`true`.
`x.equals(z)`也应该返回`true`

- 一致性: 如果和y引用的对象没有发生变化，反复调用`x.equals(y)`应该返回同样的结果。

- 对于任意非空引用x，`x.equals(null)`应该返回false

### 建议实现

1. 检测`this`与`otherObject`是否引用同一个对象:

```java
if (this == otherobject) return true
```

这条语句只是一个优化。实际上,这是一种经常采用的形式。因为计算这个等式要比一个一个地比较类中的域所付出的代价小得多。

2. 检测`otherObject`是否为`null`,如果为`null`,返回`false`。这项检测是很必要的。

```java
if(otherObject == null) return false;
```

3. 比较`this`与`otherobject`是否属于同一个类。如果`equals`的语义在每个子类中有所改变,就使用`getClass`检测

```java
if (getClass() != otherObject.getClass()) return false;
```

如果所有的子类都拥有统一的语义,就使用`instanceof`检测

```java
if (!(otherObject instanceof ClassName)) return false
```

4. 将`otherObject`转换为相应的类类型变量

```java
ClassName other = (ClassName) otherObject;
```

5. 现在开始对所有需要比较的域进行比较了。使用=比较基本类型域,使用equals比较对象域。如果所有的域都匹配,就返回true;否则返回false。

```java
return field1 == other.field1 8& Objects.equals(field, other.field2)
```
如果在子类中重新定义equals,就要在其中包含调用super.equals(other)。

### `hashCode` 方法

散列码（hash code）是由对象导出的一个整型数值。散列码是没有规律的。

如果一个对象没有对`hashCode`方法进行重写，那么其返回的是该对象的存储地址。如果`equal` 方法被重写了，那么就必须重新定义`hashCode`方法。

重写`hashCode`方法，需要满足以下规则：

1. `hashCode`方法应该返回一个整型数值
2. 如果存在`x.equal(y)`,那么`x.hashCode()`就必须等于`y.hashCode()`

> 注：散列码并不唯一，如果两个不同的对象，其散列码有可能相同

### `toString` 方法

## 内部类

内部类可以访问该类定义所在的作用域中的数据，包括私有数据

### 成员内部类

1. 基础语法

```java
class OuterClass {
    int outerClassProperty = 0;
    static class StaticInnerClass {
        // ...
    }
    class InnerClass {
        // ...
    }
}

// 例子
package beginner.cls.inner;

class OuterClass {
    String outerClassProperty = "outerClassProperty";
    static class StaticInnerClass {
        String staticInnerClassProperty = "staticInnerClassProperty";
    }
    class InnerClass {
        String innerClassProperty = "staticInnerClassProperty";

        String getOuterClassProperty(){
            return OuterClass.this.outerClassProperty;
        }
    }
    public static void main(String[] args){
        OuterClass outerClass = new OuterClass();

        OuterClass.InnerClass innerClass = outerClass.new InnerClass();
        System.out.println(innerClass.getOuterClassProperty());

        OuterClass.StaticInnerClass staticInnerClass = new OuterClass.StaticInnerClass();
        System.out.println(staticInnerClass.staticInnerClassProperty);
    }
}
```
2. 内部类中访问外部类属性

```java
OuterClass.this.outerClassProperty
```
3. 实例化内部类

```java
OuterClass outerClass = new OuterClass();

// 非静态内部类
OuterClass.InnerClass innerClass = outerClass.new InnerClass();

// 静态内部类
OuterClass.StaticInnerClass staticInnerClass = new outerClass.StaticInnerClass();
```

### 局部内部类(todo)

> todo

### 匿名内部类 

> [Anonymous Inner Class in Java](https://www.geeksforgeeks.org/anonymous-inner-class-java/)

## Java 枚举

> [Java Enums](http://tutorials.jenkov.com/java/enums.htm)

在Java中枚举对象可以在类外或类中声明，但不可在方法中声明

```java
enum Color{ 
    RED, GREEN, BLUE; 
} 
  
public class Test{ 
    // Driver method 
    public static void main(String[] args) 
    { 
        Color c1 = Color.RED; 
        // output: RED
        System.out.println(c1); 
    } 
}
```

### 枚举类 

```java
public enum Level {
    HIGH  (3),  //calls constructor with value 3
    MEDIUM(2),  //calls constructor with value 2
    LOW   (1)   //calls constructor with value 1
    ; // semicolon needed when fields / methods follow

    private final int levelCode;

    private Level(int levelCode) {
        this.levelCode = levelCode;
    }
}
```

## Java 接口

```java
[public] interface InterfaceName{
    // declare constant fields
    double EXAMPLE_FINAL = 3.14;
    // declare methods that abstract
    void exampleAbstractMethod();
    // declare default methods.
}
```
- 成员方法修饰符只能是`public`，接口中所有方法都自动属于`public`。
- 接口中不能包含实例域，但却可以包含常量。

```java
public Interface ExampleInterface{
    double SPEED_LIMIT = 95;
}
```
接口中的域被自动设置成`public static final`

- 可以使用`instanceof`来检查一个对象是否实现了某个特定的接口

```java
if(anObject instanceof Comparable){ ... }
```
- 一个类可以实现多个接口

### 静态方法

在Java 8中，允许在接口中增加静态方法。

```java
public Interface ExampleInterface{
    public static void functionName(){
        ....
    }
}
```

### 默认方法

可以为接口方法提供一个默认实现，必须用`default`修饰符标记这个方法

```java
public interface ExampleInterface{
    default int exampleDefaultFunction(){
        // ....
        return 0;
    }
}
```

#### 默认方法冲突的解决

- **超类优先：** 如果超类和接口中，都提供了同一方法（方法签名一样），那么接口中的默认方法将被忽略(类优先原则)
- **接口冲突：** 如果存在多个接口提供了相同的默认方法，则必须指定具体的接口来解决冲突

```java
interface Named{
    default String getName(){
        return "Named";
    }
}

interface Person{
    default String getName(){
        return "Person";
    }
}

class Student implements Person,Named{
    public String getName(){
        // 编译器无法知道要调用哪个接口的默认方法
        // return Person.super.getName();
        return super.getName();
    }
}
class Main {
  public static void main(String[] args) {
    Student student = new Student();
    System.out.println(student.getName());
  }
}

```

### 函数式接口

函数式接口是lambda表达式的基础，函数接口有2 条重要法则：

1. 一个函数接口有且仅有一个抽象方法。

这里需要注意的是，这里的唯一的抽象方法不应与Object类中的公共方法有相同的签名。

```java
interface FunctionInterface{
    int hashCode();
}
```
由于`hashCode`是属于`Object`公共方法，所以这个接口不是函数接口

2. 函数式接口中可以额外定义多个抽象方法，但这些抽象方法签名必须和Object的public方法一样。

任何满足单一抽象方法法则的接口，都会被自动视为函数接口。

#### 内置函数接口

在Java中内置了很多常用的函数接口：

| 函数式接口      | 参数类型 | 返回类型  | 抽象方法名 |
| --------------- | -------- | --------- | ---------- |
| `Function<T,R>` | T        | R         | `apply`    |
| `Comsumer<T>`   | T        | `void`    | `accept`   |
| `Predicate<T>`  | T        | `boolean` | `test`     |

#### 自定义函数接口

```java
@FunctionalInterface
public interface FunctionInterfaceName{
    // abstract function
    // 比如：T transform(T input);
}
```

### Lambda表达式

在任何需要<b>函数接口</b>的地方，我们都有 3 种选择:

1. 传递一个匿名内部类
2. 传递一个 lambda 表达式。
3. 在某些情况下传递一个方法引用而不是lambda表达式。

我们以数组的排序为例：

`sort`方法需要传递一个实现了`Comparator`接口的对象，过往我们常常使用匿名内部类来达到我们的目的：

```java
Integer arr[] = {2,5,10,8,4};
Arrays.sort(arr,new Comparator<Integer>(){
    public int compare(Integer a, Integer b){
        // do something
        return -1;
    }
});
```

在Java 8 中我们可以使用`lambda`表达式来进一步简化：

```java
Arrays.sort(arr,(a,b)->{
    // do something
    return -1;
});
```

虽然`lambda`表达式十分简洁，但并不是所有的匿名内部类都可以用`lambda`替代的，使用`lambda`的前提条件是<b>必须有相应的函数接口</b>。

### 方法引用

在上一节，我们介绍了处理函数式接口实现的两种方式，实际上如果已经有现成的方法可以使用，我们还可以直接传入方法引用：

```java
public class InterfaceDemo{

    public static void main(String[] args){
        Integer arr[] = {2,5,10,8,4};
        Arrays.sort(arr,InterfaceDemo::cmp);
    }

    static int cmp(int a,int b){ return -1; }
}
```
上述代码在`Arrays.sort()`中直接传入了静态方法`cmp`的引用，用`InterfaceDemo::cmp`表示。

> 所谓方法引用，是指如果某个方法的参数和返回值恰好与所要求的接口一致，就可以直接传入方法引用。

因为`Comparator<String>`接口定义的方法是`int compare(String, String)`，和静态方法`int cmp(String, String)`相比，除了方法名外，方法参数一致，返回类型相同，因此，可以直接把方法名作为Lambda表达式传入。

方法引用存在以下三种形式：

1. `object::instanceMethod`，比如`new Timer(1000,System.out::println)`，等价于`(x)->System.out.println(x)`

传入<b>对象的实例方法</b>

2. `Class::staticMethod`

传入<b>类的静态方法</b>，比如例子中的`InterfaceDemo::cmp`，等价于`(a,b)->InterfaceDemo.cmp(a,b)`。

3. `Class::instanceMethod`

传入<b>类的实例方法</b>，比如`Arrays.sort(strings,String::compareToIgnoreCase)`，等价于`(x,y)->x.compareToIgnoreCase(y)`。

## Java 包

Java中的包用于将类组织起来，并确保类的唯一性。

### 包的导入

包的导入有两种方式：

- 类前面添加完整的包名

```
java.time.LocalDate today = java.time.LocalDate.now();
```

- 使用`import`

```
import java.util.*;

LocalDate today = LocalDate.now();
```

需要明确的是：只能使用`*`导入一个包，并且只能导入包文件夹下一级目录下的类，无法访问子包中的类，比如`com.demo.*`，只能导入`com/demo/*.java`的类

如果存在同名的类，则需要在每个类前面加一个完整的包名。

### 包的声明

```
package packageName;
```

### 包的作用域

标记为`public`的部分可以被任意类使用，标记为`private`的只能被定义它们的类使用。如果没有定义访问修饰符，则这个部分可以被同一个包的所有方法使用。

## Java异常

<img src="./Assets/java-exception.png" alt="Java Exception" style="zoom:50%;" />

所有的异常都是由`Throwable`继承而来的，异常可以分为两类：`Error`和`Exception`。

`Error`描述的是Java运行时系统内部错误和资源耗尽情况。

`Exception`可分为`RuntimeException`和`其他异常`（如IOException）。由程序编写错误导致的异常属于RuntimeException，而程序本身没有问题，但由于I/O错误导致的异常属于其他异常。

在Java中，异常也可分为`受查异常`（Checked Exception）和`非受查异常`(Unchecked Exception)。

`受查异常`即我们需要对其处理或向上传递的异常，比如`IOException`。

`非受查异常`指我们无需处理或我们无力处理的异常，`Error`和`RuntimeException`属于`非受查异常`。


## 类路径

类路径：顾名思义就是用于存储类的文件位置。

在Linux系统中，可以使用`env | grep CLASSPATH`或者`echo $CLASSPATH`查看已有类路径。

在Unix环境中，类路径中的不同项目之间采用冒号（:）分隔：

```bash
CLASSPATH=.:/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home/lib/tools.jar
```

在Windows环境中，使用分号（;）分隔：

```bash
.;/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home/lib/dt.jar;/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home/lib/tools.jar
```

在代码中，我们可以使用`System.getProperty("java.class.path")`来获取类路径

### 设置类路径

可以使用`-classpath`（或`-cp`）选项指定类路径：

```bash
java -classpath /home/user:/home/local
```

设置是需要注意Unix系统和Window系统的区别。

## Java 自定义注解

Java中使用`@interface`来定义注解：

```java
public @interface IAnnotion{}
```

注解中还可以定义参数：

```java
public @interface IAnnotion{
    String value() default "";
    String name() default "demo";
}
```

### 元注解

我们把用于修饰注解的注解称之为元注解（meta annotation）

- `@Target`: 定义注解可修饰的元素类型

```java
@Target(ElementType.METHOD)
public @interface IAnnotion{
    String value() default "";
    String name() default "demo";
}
```

`ElementType`是一个枚举，常用的值包括：

1. `TYPE`:  指明注解可用于类/接口/枚举
2. `FIELD`: 表示注解可用于类字段
3. `METHOD`: 表示注解可用于方法

- `@Retention`: 定义注解的生命周期

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface IAnnotion{
    String value() default "";
    String name() default "demo";
}
```

`RetentionPolicy`也是一个枚举类，其值包括：

1. `SOURCE`:  仅在源文件中有效，编译器忽略，比如`@Override`
2. `CLASS`:  随源文件一起编译在class文件中，运行时忽略
3. `RUNTIME`: 在运行时有效

- `@Repeatable`: 指明改注解是否可以重复

经过`@Repeatable`修饰后，某个类型声明处，可多次使用

```java
@Repeatable(Colors.class)
@interface Color{
    String value() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@interface Colors{
    Color[] value();
}

@Color(value = "RED")
@Color(value = "BLACK")
public class AnnotationDemo{
}
```

- `@Inherited`: 定义子类是否可以继承父类定义的注解

```java
@Target(ElementType.TYPE)
@Inherited
@Retention(RetentionPolicy.CLASS)
public @interface IAnnotion{
    String value() default "";
    String name() default "demo";
}

@IAnnotion
public class Car{}

// 这里SUV继承了CAR，也会被IAnnotion修饰
public class SUV extends Car{
}
```

## `System.getProperty()` vs `System.getenv()`

`System.getenv()`和`System.getProperty()`是`Java`常用的两种读取环境配置信息的方法，它们都是`java.lang.System`类的方法。

1. `System.getenv()`

`System.getenv()`读取的是当前操作系统的环境变量

```java
System.getenv().forEach((k, v) -> {
    System.out.println(k + ":" + v);
});
```

这些属性是只读的，我们无法在运行时对这些环境进行修改

2. `System.getProperty()`

`System.getProperty()`读取的是当前用户、系统、JVM等相关信息，以及在运行Java程序时以

```bash
# 注意：设置属性前要加-D
java -jar package.jar -DpropertyName=value
```
方式传入的参数。

与`System.getenv()`不同的是，`System.getProperty()`所获取的属性，可以通过`System.setProperty(key,value)`来修改，并只影响当前程序。
