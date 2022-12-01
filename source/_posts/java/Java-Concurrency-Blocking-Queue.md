---
title: Java  阻塞队列和并发队列
description: 在java多线程应用中，队列的使用非常频繁，Java提供的线程安全的Queue可以分为阻塞队列和非阻塞队列
categories: 
- Java
---

在java多线程应用中，队列的使用非常频繁，Java提供的线程安全的Queue可以分为阻塞队列和非阻塞队列。

## 阻塞队列

阻塞队列（`BlockingQueue`）与普通队列之间最大的不同点在于阻塞队列存在以下两个特点：

1. 在队列为空时，获取元素的线程会会阻塞直到队列变为非空。
2. 当队列满时，存储元素的线程阻塞直到队列可用。

阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。

### 阻塞队列的常用方法

#### 添加类方法

- `add(e)`: 添加成功返回`true`, 如果当前队列已满，则抛出异常`IllegalStateException``
- `offer(e)`: 添加成功返回`true`, 如果当前队列已满，则返回`false`
- `put(e)`:  添加成功则将元素添加到队尾，如果当前队列已满，则阻塞当前线程直到队列空出

#### 删除类方法

- `remove()`：返回并移除队列的第一个元素，若队列为空返回`false`
- ``poll()`: 返回并移除队列的第一个元素，若队列为空返回`null`
- `take()`: 返回并移除队列的第一个元素，若队列为空则阻塞

#### 获取类方法

- `element()`: 获取但不移除队列的第一个元素，没有元素则抛出异常
- `peek()`: 获取但不移除队列的第一个元素，没有元素则返回null

![java-concurrency-blocking-queue](/images/java/java-concurrency-blocking-queue.webp)

### 常用阻塞队列

### `ArrayBlockingQueue`

`ArrayBlockingQueue`是基于数组实现的有界队列。构造方法如下：

```java
public ArrayBlockingQueue(int capacity) { ... }

public ArrayBlockingQueue(int capacity, boolean fair) { ... }

public ArrayBlockingQueue(int capacity, boolean fair,
                          Collection<? extends E> c) { ... }
```

参数`fair`用于设置线程是否公平访问队列。所谓公平访问是指阻塞的线程，可以按照阻塞的先后顺序访问队列，即先阻塞线程先访问队列。非公平性是对先等待的线程是非公平的，当队列可用时，阻塞的线程都可以争夺访问队列的资格，有可能先阻塞的线程最后才访问队列。为了保证公平性，通常会**降低吞吐量**。

> [非公平锁与公平锁](https://www.jianshu.com/p/f584799f1c77)

`ArrayBlockingQueue`的添加和删除操作共用同一个锁对象，由此意味着添加和删除无法并行行，这点不同于`LinkedBlockingQueue`。

通常`ArrayBlockingQueue` 比 `LinkedBlockingQueue` 更加常用，因为前者在添加或删除元素时不会
产生或销毁任何额外的 Node(节点)实例，而后者会生成一个额外的Node 实例。在长时间、高并
不可用发处理大批量数据的场景中，`LinkedBlockingQueue` 产生的额外Node实例会加大系统的GC 压力。

### `LinkedBlockingQueue`

> 此队列的默认和最大长度为`Integer.MAX_VALUE`

`LinkedBlockingQueue`是基于链表实现的有界阻塞队列。其内部维持着一个数据缓冲队列(该队列由一个链表构成)。`LinkedBlockingQueue` 对于添加和删除元素分别采用了独立的锁来控制数据同涉，这也意味着在高并发的情况下，生产者和消费者可以并行地操作队列中的数据，以此来提高整个队列的并发性能。

### `PriorityBlockingQueue`

`PriorityBlockingQueue`是一个支持优先级的**无界阻塞队列**。默认情况下元素采取自然顺序升序排列。也可以自定义类实现`compareTo()`方法来指定元素排序规则，或者初始化`PriorityBlockingQueue`时，指定构造参数`Comparator`来对元素进行排序。需要注意的是不能保证同优先级元素的顺序。

### `DelayQueue`

`DelayQueue`是一个支持延时获取元素的无界阻塞队列。队列使用PriorityQueue来实现。队 列中的元素必须实现`Delayed`接口，在创建元素时可以指定多久才能从队列中获取当前元素。只有在延迟期满时才能从队列中提取元素。

`DelayQueue`非常有用，可以将`DelayQueue`运用在以下应用场景

1. 缓存系统的设计：可以用`DelayQueue`保存缓存元素的有效期，使用一个线程循环查询`DelayQueue`，一旦能从DelayQueue中获取元素时，表示缓存有效期到了。
2. 定时任务调度：使用DelayQueue保存当天将会执行的任务和执行时间，一旦从`DelayQueue`中获取到任务就开始执行，比如TimerQueue就是使用DelayQueue实现的。

>  如何使用可以看这两篇文章:
>
> [使用DelayQueue模拟订单自动取消功能]( [https://blog.csdn.net/ouyunwen/article/details/82383726](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fouyunwen%2Farticle%2Fdetails%2F82383726))
>
> [基于Redis实现DelayQueue延迟队列设计方案(这篇比较全面)]( https://www.jianshu.com/p/df2c0a6baca4)

### `SynchronousQueue`

`SynchronousQueue`是一个不存储元素的阻塞队列。每一个`put`操作必须等待一个take操作，否则不能继续添加元素。
 它支持公平访问队列。默认情况下线程采用非公平性策略访问队列。

`SynchronousQueue`可以看成是一个传球手，负责把生产者线程处理的数据直接传递给消费者线程。队列本身并不存储任何元素，非常适合传递性场景。SynchronousQueue的吞吐量高于`LinkedBlockingQueue`和`ArrayBlockingQueue`。

## 并发队列



## 参考

- [10分钟搞定 Java 并发队列好吗？好的](https://segmentfault.com/a/1190000023769855)
- [关于阻塞队列在java7中的七种提供和区别](https://www.jianshu.com/p/9e4eca73553c)

