---
title: screen 命令相关
date: 2023-07-23 22:41:00
categories:
  - Linux
tags:
  - screen
  - Linux
  - 终端多窗口
  - 会话
  - 高级用法
description: 'screen是一个非常强大的终端多窗口工具,可以实现在一个终端内同时连接多个终端会话。'
cover: https://s2.loli.net/2023/07/24/kLHNvzpIYK5SU2h.webp
---

## screen命令基本用法

-  启动一个screen会话:

```bash
screen
```

- 退出screen会话:ctrl + a + d- 查看当前所有的screen会话:

```bash
screen -ls
```

- 重新连接一个screen会话:

```bash
screen -r sessionid
```

- 强制重新连接一个screen会话:

```bash
screen -dr sessionid
```

## screen会话管理

- 创建一个命名的screen会话:

```bash
screen -S sessionname
```

- 在一个screen会话中创建一个新的窗口:ctrl + a + c - 在screen窗口间切换:ctrl + a + 窗口编号- 暂时断开一个screen会话:ctrl + a + d- 杀死一个screen会话:

```bash
screen -X -S sessionid quit
```

- 杀死所有断开的screen会话:

```
screen -wipe
```

## screen高级用法

- 监控一个进程:

```bash
screen -dmS sessionname command
```

- 同时启动多个screen会话:

```
screen -d -m -S session1 command1
screen -d -m -S session2 command2
```

- 录制screen会话:

```
screen -L -S sessionname
```

- 播放recorded会话:

```
screen -r -L sessionname
```

