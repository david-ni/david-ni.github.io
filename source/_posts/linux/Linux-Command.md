---
title: Linux 常用命令
description: Linux 常用命令
categories: 
- Linux
---
### 查看Linux发行版本

```bash
lsb_release -a
```

### `PATH`的设置

- Mac OS X

打开`/etc/paths` ，添加新的路径，重启terminal

![image](/images/linux/103116963-51262500-46a3-11eb-8285-976398522ca3.png)

- Linux

1. Open the .bashrc file in your home directory (for example, /home/your-user-name/.bashrc) in a text editor.
2. Add export PATH="your-dir:$PATH" to the last line of the file, where your-dir is the directory you want to add.
3. Save the .bashrc file.
4. Execute command: `source .bashrc`

### 查看文件内容

文件内容查看的命令有很多，适用于不同场景

| 命令                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| `cat [-AbEnTv] filename`     | 从第一行开始显示文件的全部内容                               |
| `tac filename`               | 从最后一行开始显示文件全部内容                               |
| `more filename`              | 一页页显示文件内容<br />- `空格键`：代表向下翻页<br />- `Enter`: 代表向下翻一行<br />- `/关键字`: 向下查找关键字<br />- `b`: 代表往回翻页<br />- `q`: 代表退出 |
| `less filename`              | `less`命令与`more`功能相同，但更强大<br />- `空格键`: 代表向下翻页 <br />- `[pageDown]`: 向下翻一页<br />-` [pageUp]`: 向上翻一页<br />- `/关键字`: 向下查找关键字<br />- `?关键字`: 向上查找关键字<br />- `g`: 前进到该文件的第一行<br />- `G`: 前进到该文件的最后一行<br />- `q`: 代表退出 |
| `head [-n number] filename`  | 取出前面几行                                                 |
| `tail [-fn number] filename` | 取出后面几行                                                 |

### 修改文件权限

```bash
chmod [options] mode file...
```

> 推荐工具网站：[Chmod Calculator](https://chmodcommand.com/) 生成chmod命令

Linux 将文件调用权限分为三级: `文件所有者（Owner）`、`用户组（Group）`和`其它用户（Other Users）`。

![linux-command-chomd-1](/images/linux/100578178-aaa56900-331c-11eb-8b9f-d34ca8b13b57.jpg)

只有文件所有者和超级用户可以修改文件或目录的权限。可以使用`ll`或`ls -l`来查看目录下文件的权限。

常用的几种常用的八进制数字模式：

| Mode | Descript                                |
| ---- | --------------------------------------- |
| 777  | 该文件/目录对所有用户可读、可写、可执行 |

### 管道

| 管道   | 描述                                                         | 举例                           |
| ------ | ------------------------------------------------------------ | ------------------------------ |
| `cut`  | 按关键字分割<br />选项与参数：<br />-`-d`: 后面接分割的字符，与`-f`一起使用，表示将信息按分割字符串分割<br />- `-f`: 根`-d`分割出的字段，取出指定字段  <br /> | `echo $PATH | cut -d ':' -f 2` |
| `grep` | 按关键字过滤<br />选项与参数：<br />- `-i`:  忽略大小写<br />- `-v`: 反向选择 | `pa aux | grep 关键字`         |
| `sort` | 排序                                                         |                                |
| `uniq` | 去重                                                         |                                |
| `wc`   | 统计文件行/字/字符数量<br />- `-l` 统计行<br />- `-w` 统计字<br />- `-m` 统计字符 | `cat filename | wc`            |



### 数据流重定向

```bash
commond > outfile 2>&1 &
```

> 关于`2>&1`的用法，[stackoverflow](https://stackoverflow.com/questions/818255/in-the-shell-what-does-21-mean?answertab=active#tab-top)上有解释

标准输出和错误输出都将被重定向到一个叫做outfile的文件中，举个例子：

```bash
java -jar example.war > log.log 2>&1 &
```

#### >> 和 >的区别

```bash
echo "xxx" > filename 
vs 
echo "xxx" >> filename
```

- `>` 覆盖目标文件的内容
- `>>`追加到目标文件的末尾

### `systemctl`命令

`systemctl`是 Systemd 的主命令，用于管理系统和服务。

> [Systemd 入门教程：命令篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

> [Systemd wiki](https://wiki.archlinux.org/index.php/systemd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

```bash
systemctl [OPTIONS...] COMMAND [UNIT...]
```

