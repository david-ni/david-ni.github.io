---
title: Java中的线程
description: 本文主要介绍Java线程
categories: 
- Java
---

**线程**（thread）是操作系统能够进行运算调度的最小单位。同一时间，单个CPU只能执行一个线程。而**进程（Process）**是系统进行资源分配和调度的基本单位，一个进程中可以包含多个线程。

上面解释可能过于抽象，有兴趣的同学可以参考，阮一峰老师的[进程与线程的一个简单解释](https://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)，相信对大家理解线程和进程会有很大的帮助。

每个Java程序至少存在一个进程和至少两个线程（主线程和GC线程）。当一个 Java 程序启动时，JVM 会创建主线程，并在该线程中调用程序的 main() 方法。

![main-thread-in-java](/images/java/main-thread-in-java.jpeg)

如上图，JVM 除了创建主线程之外还默默的创建了其它线程，例如，与垃圾收集、对象终止和其它 JVM 内务处理任务相关的线程。

> 线程和进程的区别:

- 进程相对独立，不同进程之间很难共享内存。而线程虽然有它自己的堆栈、自己的程序计数器和自己的局部变量。但是，与分隔的进程相比，进程中的线程之间的隔离程度要小。它们共享内存、文件句柄和其它每个进程应有的状态；
- 一个线程挂掉将导致整个进程崩溃，而不同进程之间一般不影响；
- 进程要比线程消耗更多的计算机资源；

## 线程的创建

在Java中，线程创建有四种方式：

1. 继承`Thread`类

```java
public class ChildThred extends Thread{
    @Override
    public void run(){
        System.out.println(Thread.currentThread().getName());
    }
}
```

2. 实现一个`Runnable`接口

```java
Runnable runnable = () -> {
    System.out.println(
        Thread.currentThread().getName()            
    );
};

Thread childThread = new Thread(runnable);
```

3. 使用`FutureTask`创建线程

```java
FutureTask task = new FutureTask<>(()->{
		return null;
});
        
Thread thread = new Thread(task);    
thread.start();
//task.get();
```

4. 通过线程池创建

```java
ExecutorService pool = Executors.newFixedThreadPool(2);

pool.submit(()->{
	//....
});
```

## 线程的优先级

同一时间，单个CPU只能执行一个线程，那么CPU是如何被“共享”的呢？目前线程的调度模型主要有两种：**分时调度**和 **抢占式调度**。

- **分时调度**：系统平均分配CPU时间片，所有线程轮流占用CPU
- **抢占式调度**：系统按照线程优先级分配CPU时间片，优先级高的线程优先分配到CPU使用权

目前大部分操作系统使用的是**抢占式调度**。在java中可以使用`setPriority(int newPriority) `来配置线程的优先级，一般来说优先级越高，则线程获取CPU时间片的机会越多。

> 在java中，优先级的范围为 `1～10`,默认的优先级为`5`

## 线程的生命周期

![main-thread-in-java-lifecycle](/images/java/main-thread-in-java-lifecycle.png)

Java中线程的生命周期分为6种状态：

```java
public enum State {
  NEW,
  RUNNABLE,
  BLOCKED,
  WAITING,
  TIMED_WAITING,
  TERMINATED;
}
```

1. **创建**（`NEW`）

当线程创建成功但**没有调用`start` 方法的线程**都处于本状态

2. **可执行/运行中**（`RUNNABLE`）

当我们创建线程并执行`start`方法时，线程切换成可执行（Runnable）状态，等待CPU的调度。

```java
thread.start();
```

但处于本状态并不意味着线程正在执行，只有当该线程获得CPU时间片，线程中的`run`方法才得以真正执行。

3. **阻塞**（`BLOCKED`）

   处于本状态的线程不会占用CPU资源，以下情况会让线程进入阻塞状态：

   - 线程等待获取锁
   - IO阻塞，包括磁盘IO、网络IO

4. **无限等待**（`WAITING`）

   处于本状态的线程不会占用CPU资源，需要被其他线程唤醒才能进入就绪状态。以下几种方式可以让线程进入本状态：

   - `Object.wait()`方法，对应的唤醒方式为：`Object.notify()`/`Object.notifyAll()`
   - `Thread.join()`方法，被合入的线程执行完后改线程被唤醒
   - `LockSupport.park()`方法，对应的唤醒方式为：`LockSupport.unpark()`

5. **限时等待**(`TIMED_WAITING`)
   - `Thread.sleep(time)`
   - `Thread.join(time)`
   - `LockSupport.park(time)`
6. **终止**（`TERMINATED`）

终止线程的方式有以下几种：

- 自然终止：当线程中没有可运行的程序时，线程自动终止;
- 异常终止：如果在`run`方法执行过程中发生异常而没有被捕获，线程异常终止
- 通过调用`interrupt`方法

## 线程中的方法

### `interrupt`

java提供了`stop()`方法（已被废弃）来**强制**终止线程，正如我们不推荐直接按电源关机一样，使用`stop`方法同样存在风险。在程序中，我们不能任意的终止线程，它可能正在操作数据库，强行终止可能会导致数据的不一致；也有可能它持有某把锁，突然的中断可能导致锁不能释放。

而相对而言`interrput`则更加温和，调用此方法时，存在以下情况：

1. 如果此线程被`Object.wait()`、`join`以及`sleep`这三个方法阻塞，线程会立马退出，并抛出`InterruptedException`异常
2. 如果此线程正在运行，则不受影响，仅将线程的中断标记设置为`true`

```java
/**
 * 线程调用interrupt,但while循环将继续执行直到结束
 */
public static void main(String[] args) {
    Thread thread = new Thread(new Runnable() {
        @Override
        public void run(){
            int i = 0;
            while (i < 100000000){
                i++;
            }
            System.out.println(currentThread().getName() + " done!");
        }
    });
    thread.start();
    thread.interrupt();
    System.out.println(thread.isInterrupted());
}
```

### `join`

当一个线程需要依赖另一个线程的结果时，可使用`join`方法

```java
/**
 * 线程B只有在线程A执行完后才继续执行
 */
public class JoinThreadTest {
    public static void main(String[] args) {
        Thread threadA = new Thread(()->{
            System.out.println("threadA running");
            try {
                Thread.sleep(5000);
                System.out.println("threadA done");
            }catch (InterruptedException exception){
                exception.printStackTrace();
            }
        });

        Thread threadB = new Thread(()->{
            System.out.println("threadB running");
            try {
                threadA.join();
                System.out.println("threadB done");
            }catch (InterruptedException exception){
                exception.printStackTrace();
            }
        });

        threadA.start();
        threadB.start();
    }
}
```

### `yield`

线程`yield`操作的作用是让目前正在执行的线程放弃当前的执行，**暂时**让出CPU的执行权限，使得CPU可以去执行其他线程，但被执行让步的线程状态依旧是`RUNNABLE`，只不过是让出了CPU时间片。

线程`yield`时，线程放弃和重占CPU的时间是不确定的，可能刚刚放弃CPU，又马上获取了CPU的执行权限。

总结起来，`Thread.yeild`方法有以下特点：

- `yield`仅能使一个线程从运行状态转化到就绪状态，而不是阻塞状态
- `yield`不能保证使得当前正在运行的线程迅速转换到就绪状态
- 即使完成切换，系统通过线程调度机制从所有就绪线程中挑选下一个执行线程时，该就绪线程也有可能被选中

### 守护线程

java中线程分为两类：守护进程和用户进程。守护进程也分为后台进程。

> 使用实例方法`setDaemon`将线程标记为守护进程和用户进程

要点：

- 进程终止顺序：用户进程全部终止后，JVM虚拟机进程也随之终止，之后守护进程也随之终止。
- 守护进程必须在启动前将其守护状态设置为`true`
- 守护进程存在被JVM强制终止的风险，所以在守护进程中尽量不要去访问系统资源，比如文件句柄、数据库链接等。守护进程被强制终止时，可能会导致系统资源不负责任的中断，从而导致资源不可逆的损坏
- 守护线程创建的线程也是守护线程

## 线程池



## 参考

- [Java 线程简介](https://www.ibm.com/developerworks/cn/education/java/j-threads/j-threads.html)
- [Life cycle of a Thread](https://www.javatpoint.com/life-cycle-of-a-thread)
- [Life Cycle of a Thread in Java](https://www.baeldung.com/java-thread-lifecycle)
- [Killing threads in Java](https://www.geeksforgeeks.org/killing-threads-in-java/)
- [Java 8 Concurrency Tutorial: Threads and Executors](https://winterbe.com/posts/2015/04/07/java8-concurrency-tutorial-thread-executor-examples/)
- [Main thread in Java](https://www.geeksforgeeks.org/main-thread-java/)
- [Introduction to Thread Pools in Java](https://www.baeldung.com/thread-pool-java-and-guava)
- [线程池是怎样工作的](https://mp.weixin.qq.com/s/VTBj0KS_vPR_YY2imJYYFw)
- [线程池ThreadPoolExecutor实现原理](https://juejin.im/post/5aeec0106fb9a07ab379574f)
- [深入理解Java线程池：ThreadPoolExecutor](http://www.ideabuffer.cn/2017/04/04/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Java%E7%BA%BF%E7%A8%8B%E6%B1%A0%EF%BC%9AThreadPoolExecutor/#addWorker%E6%96%B9%E6%B3%95)
- 《java8高并发核心编程》