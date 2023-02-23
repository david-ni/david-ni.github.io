# Js事件循环

对于`Event Loop` HTML Standard有如下定义：

> To coordinate events, user interaction, scripts, rendering, networking, and so forth...Each agent has an associated event loop, which is unique to that agent.

简而言之`Event Loop`是一个任务调度中心，用于协调事件、用户交互、脚本、渲染、网络请求等等这些任务。

`Event Loop`可以分为三类：

- Window Event Loop（通常所指的）
- Worker Event Loop
- Worklet Event Loop

`Window Event Loop`是最经常涉及的，也是我们后面所指代的，每打开一个浏览器界面通常就对应一个`Event Loop`，而`Worker`是Js中线程创建的方法，相当于Java中的`Thread`，`Worklet`还处于草案阶段，主要应用于需要超高性能场景（ `Worklet` 中跑的是机器码而不是Js ）。

### Task queue

一个 `Event Loop` 有一个或多个 `task queues`。一个 `task queue` 是一系列 tasks 的集合。

### Task(宏任务)

一个 task 可以有多种 `task sources` (任务源) ：

- DOM 操作任务源，比如一个元素以非阻塞的方式插入文档
- 用户交互任务源，用户操作（比如 click）事件
- 网络任务源，网络 I/O 响应回调
- history traversal 任务源，比如 history.back()

除此之外还有像 `Timers` (`setTimeout`、`setInterval`等)、`IndexDB` 操作也是 `task source`

###  Microtask（微任务）

一个 `event loop` 有一个 `microtask queue`，通常认为以下几个是 microtask：

- promises
- MutationObserver
- Object.observe

### Event Loop 过程

根据[Html Stardard](https://html.spec.whatwg.org/#event-loop-processing-model)，Event Loop的执行通常涉及到以下几个步骤：

1. 在所选 task queue (taskQueue)中约定必须包含一个可运行任务。如果没有此类 task queue，则跳转至下面 microtasks 步骤
2. 取出taskQueue 中最老的 task (oldestTask)并执行，完成后从 taskQueue 中删掉它
3. 执行 microtasks 队列中的任务
4. 设置 hasARenderingOpportunity 为 false。
5. 更新渲染。
6. 如果当前是 `window event loop` 且 task queues 里没有 task 且 microtask queue 是空的，同时渲染时机变量 hasARenderingOpportunity 为 false ，去执行 idle period（`requestIdleCallback`）。
7. 返回到第一步。

## 其他资料

- Philip Roberts 关于[Event Loop的演讲](https://www.youtube.com/watch?v=8aGhZQkoFbQ)，重点推荐
- [Event Loop 可视化工具](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D) 可以配合视频一起
- [Difference between microtask and macrotask within an event loop context](https://stackoverflow.com/questions/25915634/difference-between-microtask-and-macrotask-within-an-event-loop-context)
- [Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/?utm_source=html5weekly)
- [从Promise来看JavaScript中的Event Loop、Tasks和Microtasks](https://github.com/creeperyang/blog/issues/21)
- [从 event loop 规范探究 javaScript 异步及浏览器更新渲染时机](https://gitlwz.github.io/2019/04/03/js-requestIdleCallback/)

