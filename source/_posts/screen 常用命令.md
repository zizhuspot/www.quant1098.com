---
title: screen Command Related
date: 2023-07-23 22:41:00
categories:
  - Linux
tags: 
  - screen
  - Linux
  - Terminal Multi-Window
  - Sessions
  - Advanced Usage
description: screen is a very powerful terminal multi-window utility that allows you to connect to multiple terminal sessions in a single terminal at the same time.
cover: https://s2.loli.net/2023/07/24/kLHNvzpIYK5SU2h.webp
---

## Basic screen command usage

-  Start a screen session.

```bash
screen
```

- Exit a screen session: ctrl + a + d- View all current screen sessions.

```bash
screen -ls
```

- Reconnecting a screen session.

```bash
screen -r sessionid
```

- Force reconnection of a screen session.

```bash
screen -dr sessionid
```

## Screen session management

- Creates a named screen session:

```bash
screen -S sessionname
```

- Create a new window in a screen session: ctrl + a + c - Switch between screen windows: ctrl + a + window number - Temporarily disconnect a screen session: ctrl + a + d - Kill a screen session.

```bash
screen -X -S sessionid quit
```

- Kill all disconnected screen sessions.

```
screen -wipe
```

## Advanced Screen Usage

- Monitor a process.

```bash
screen -dmS sessionname command
```

- Start multiple screen sessions at the same time.

```
screen -d -m -S session1 command1
screen -d -m -S session2 command2
```

- Record a screen session.

```
screen -L -S sessionname
```

- Play Recorded Sessions.

```
screen -r -L sessionname
```

