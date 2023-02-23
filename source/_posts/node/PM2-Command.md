# PM2 命令集

## 管理自启动应用

PM2 生成启动脚本并在开机后自启动指定的进程，具体可参见[Startup Script Generator](https://pm2.keymetrics.io/docs/usage/startup/)，这里只简略介绍一下步骤：

1. 生成启动脚本

```bash
# init-system 是可选的
pm2 startup <init-system>
```

> 关于`init-system`的详细的概念可以去[Wiki](https://en.wikipedia.org/wiki/Init)上查看，这里简单说一下，我们知道Linux在启动时通常会启动很多进程，不同版本不同用途的Linux可能要求启动的进程有很大的差别，比如图形交互版的Linux和命令交互的Linux，所以我们需要一个“容器”去定义这些启动项，`init-system`就是这样的“容器”。常见的`init-system`有：`systemd`、`upStart`等

2. 保存当前`PM2`已启动的进程列表

```bash
pm2 save
```

⚠️: 在运行本命令前，你需要确保要自启动的进程已运行