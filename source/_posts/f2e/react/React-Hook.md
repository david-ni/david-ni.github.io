# React Hook

React 16.8发布Hook特性已一年有余，笔者也在多个项目中有所应用。但对React Hook内部原理还是一知半解，趁着这段时间有空，梳理成文。

 ## 为什么要使用Hooks?

> [React Hook Motivation](https://reactjs.org/docs/hooks-intro.html#motivation)

React声明定义组件的方式有两种：类组件和函数组件，但这两种方式都有缺陷。

对于函数组件而已，虽然简洁，但往往不够用：

- 没有声明周期函数
- 不支持内部state

而类组件虽然没有函数组件的烦恼，不过也存在：

- 不如函数组件简洁

- 非UI逻辑难以复用

  > [非UI逻辑难以复用的例子](https://zhuanlan.zhihu.com/p/137183261)

如何兼顾简洁，又不至于缺胳膊少腿呢？

Hooks踩着七彩祥云而来...

## Hooks 的规则

- 请不要在循环、条件或者嵌套函数中调用 Hooks
- 在 React 函数中才去调用 Hooks

## Hooks 源码解析

> hook 源码入口： packages/react/src/ReactHooks.js

以 `useState`为例：

```typescript
function useState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  const dispatcher = resolveDispatcher();
  return dispatcher.useState(initialState);
}
```

所有的 `dispatcher` 都是由 `ReactCurrentDispatcher` 这个变量维护的，而`ReactCurrentDispatcher`在[renderWithHooks](https://github.com/facebook/react/blob/v16.14.0/packages/react-reconciler/src/ReactFiberHooks.new.js#L346)中管理。

 从代码中可以看出，hook主要由三种类型：

1. [HooksDispatcherOnMount](https://github.com/facebook/react/blob/v16.14.0/packages/react-reconciler/src/ReactFiberHooks.new.js#L1817) Mount阶段
2. [HooksDispatcherOnUpdate](https://github.com/facebook/react/blob/v16.14.0/packages/react-reconciler/src/ReactFiberHooks.new.js#L1838) Update阶段
3. [HooksDispatcherOnRerender](https://github.com/facebook/react/blob/v16.14.0/packages/react-reconciler/src/ReactFiberHooks.new.js#L1859)在render阶段有更新的



### `Hooks`数据结构

先来谈谈hooks的结构：

```typescript
export type Hook = {|
  memoizedState: any,
  baseState: any,
  baseQueue: Update<any, any> | null,
  queue: UpdateQueue<any, any> | null,
  next: Hook | null,
|};
```

#### `memoizedState`

记录hook当前状态值，以下列代码为例:

```typescript
let times = 0;
function App() {
  const [state, setState] = useState("initial state");
  return (
    <div onClick={() => {
      setState(`state changed! ${++times}`);
    }}>{state}</div>
  );
}
```

初始时`memoizedState`的值为`"initial state"`，而后

#### `baseState`

#### `queue`

hook的更新队列，用于记录待更新的状态信息，同样是用链表结构

#### `next`



### `onMount`阶段

```typescript
// 非完整代码
function mountState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  const hook = mountWorkInProgressHook();
  hook.memoizedState = hook.baseState = initialState;
  const queue = (hook.queue = {
    pending: null,
    dispatch: null,
    lastRenderedReducer: basicStateReducer,
    lastRenderedState: (initialState: any),
  });
  const dispatch: Dispatch<
    BasicStateAction<S>,
  > = (queue.dispatch = (dispatchAction.bind(
    null,
    currentlyRenderingFiber,
    queue,
  ): any));
  return [hook.memoizedState, dispatch];
}
```

### `onUpdate`阶段

### `onRender`阶段

## 排疑解惑

1. 为什么不能在循环、条件或者嵌套函数中调用 Hooks？

> [(译)React hooks：它不是一种魔法，只是一个数组——使用图表揭秘提案规则](https://juejin.cn/post/6844903721688629256)

Hooks军规第一条：**循环、条件或者嵌套函数中调用 Hooks**，满脑子问号emoji

2. Hooks 是如何确定依赖变化的？

## 参考

1. [为什么顺序调用对 React Hooks 很重要？](https://overreacted.io/zh-hans/why-do-hooks-rely-on-call-order/)
2. [Rules of Hooks](https://reactjs.org/docs/hooks-rules.html)
3. [React Hooks源码解析，原来这么简单～](https://juejin.cn/post/6844904080758800392)
3. [代数效应](https://overreacted.io/zh-hans/algebraic-effects-for-the-rest-of-us/#:~:text=%E4%BB%A3%E6%95%B0%E6%95%88%E5%BA%94%E6%98%AF%E4%B8%80%E9%A1%B9,%E2%80%A6%E2%80%A6%E5%B0%9A%E5%9C%A8%E8%BF%9B%E8%A1%8C%E4%B8%AD%E3%80%82)
3. [React 技术揭秘](https://react.iamkasong.com/hooks/prepare.html)
3. [一文吃透react-hooks原理](https://juejin.cn/post/6944863057000529933#heading-26)