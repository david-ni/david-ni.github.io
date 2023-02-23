## 创建型模式

### 工厂模式

可分为简单工厂和抽象工厂。工厂模式并不直接实例化具体的类（可以设想成具体的商品），而是由第三方托管。

- **适用场景：**

> 存在多个不同类型的产品，有相对一致的属性或者行为，这种情况下可以使用工厂模式。比如我们KS日志，存在比赛日志和训练日志，日志版本不一样也会有所差异（产品不一致），但是日志的解析流程（行为）都是一样的（下载、解析、通知），这种情况下可以使用工厂方法

- **优点**：

> 1. 单一职责：每个产品都有对应的类
> 2. 满足开闭原则

- **缺点**

> 1. 如果产品类型多样，需要维护大量的产品/工厂类

- **应用案例**

> 比如spring/nestjs中的IOC(控制反转)

### 生成器模式

- **场景**

> 一个对象需要非常多的步骤才能生成，比如建造一座房子，需要打地基、固定钢筋、浇灌水泥等这一些列步骤

```java
public class HouseBuilder{
  // 地基
  public HouseBuilder buildBase(){....}
  // 墙
  public HouseBuilder buildWall(){...}
  // 浇灌水泥
  public HouseBuilder WaterCement(){....}
  public House build(){}
}

// 调用
(new HouseBuilder()).buildBase().buildWall().WaterCement().build();

```

- **优点**

> 1. 链式调用，可以分布创建对象，代码更清晰
> 2. 步骤可以根据不同场景组合生成不同属性的对象

- **应用案例**

> 1. java lomok builder

### 单例模式

单例模式是一种创建型设计模式， 让你能够保证一个类只有一个实例， 并提供一个访问该实例的全局节点。



## 结构模式

### 适配器模式

> 将一个接口转换成客户希望的另一个接口，使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)。

- **场景**

>  当你希望使用某个类，但是其接口与其他代码不兼容时，可 以使用适配器类

- **优点**

> 1.  通过引入一个适配器类来重用现有的类，无需修改现有代码结构



### 桥接模式

> 桥接是可将一个大类或一系列紧密 相关的类拆分为抽象和实现 两个独立的层次结构，从而 能在开发时分别使用。
>
> 这个定义看起来有点懵逼，我觉的桥接模式其实就是把继承改成组合的方式。
>
> 比方说有一个咖啡类，已经存在两个子类：BigSizeCoffee 和 SmallSizeCoffee，后面又来了个需求，需要假如不同的口味，最直接的方式是通过继承/实现的方式再生成对应的类，比如BigMilkCoffee、SmallMikeCoffee、BigSugarCoffee等等。但这样就需要维护非常多的类。
>
> 但在桥接模式下，可以把大小、口味都剥离出来变成依赖：
>
> ```java
> abstract class Coffee {
> 	public Coffee(ICoffeeSize sie, ICoffeeFlavor flavor ){}
> } 
> ```



## 参考

- [设计模式](https://refactoringguru.cn/design-patterns/composite)
