---
title: RabbitMQ快速入门(二) --- 二 RabbitMQ用户管理,角色管理及权限设置
date: 2023-07-23 08:26:00
categories:
  - 基础设施
tags:
  - RabbitMQ
  - 消息队列
  - 入门
  - 用户管理
  - 基础知识
  - 角色管理
  - 权限设置
description: 'RabbitMQ是一个非常流行的开源消息中间件,可以在大多数分布式系统中找到它的身影。它具有可靠、灵活、易用等优点,是构建异步处理和事件驱动架构的重要选择。 全文包括以下内容一docker安装RabbitMQ 二 RabbitMQ用户管理,角色管理及权限设置 三 如何保证消息99.99%被发送成功？四 如何通过持久化保证消息99.99%不丢失? 五 如何保证队列里的消息99.99%被消费'
cover: https://s2.loli.net/2023/07/24/1UNflTcoBgWEzC6.webp
---


因为RabbitMQ是使用docker安装的 因此需要先进入docker环境内的RabbitMQ

```shell
# 进入docker环境
sudo docker exec -it [容器名 ps.rabbitmq] /bin/bash
# 查看运行状态
rabbitmqctl status
```

![2023-07-23_070848.png](https://s2.loli.net/2023/07/23/dHYSOjvsRTq5faM.png)

## 一 用户管理

1. ### 查看用户列表

   ```shell
   rabbitmqctl list_users
   ```

   ![2023-07-23_071011.png](https://s2.loli.net/2023/07/23/h1J4agofqscKIPH.png)

2. ### 新建用户

   ```shell
   rabbitmqctl add_user developer 456789
   ```

   ![2023-07-23_071316.png](https://s2.loli.net/2023/07/23/3gVyCc76uRDOT8s.png)

3. ### 删除用户

   ```shell
   rabbitmqctl delete_user developer
   ```

   ![2023-07-23_071444.png](https://s2.loli.net/2023/07/23/s6FTgW58u7jAHPx.png)

4. ### 修改密码

   ```shell
   rabbitmqctl change_password developer developer123456
   ```

   ![2023-07-23_071701.png](https://s2.loli.net/2023/07/23/qz1QIem2J4Avyjp.png)

‍

## 二 角色设置

1. RabbitMQ中主要有administrator，monitoring，policymaker，management，impersonator,none几种角色。

   默认的用户guest是administrator角色，新建的developer用户没有设置角色，即为none，如果我们想把developer用户设置为administrator角色

   ```shell
   rabbitmqctl set_user_tags developer administrator
   ```

   ![2023-07-23_071855.png](https://s2.loli.net/2023/07/23/sovTtn8EAhJc1ZH.png)

   也可以给用户设置多个角色，如给用户developer设置administrator，monitoring：

   ```shell
   rabbitmqctl set_user_tags developer administrator monitoring
   ```
   ![2023-07-23_072002.png](https://s2.loli.net/2023/07/23/sAXQuOeCZln73tV.png)

​       

## 三 权限配置

现在这个developer账号并没有管理configure, write , read的权限 我们需要加上他

![2023-07-23_072700.png](https://s2.loli.net/2023/07/23/RTLsSZYNmoiXEnD.png)

1. ```shell
   rabbitmqctl set_permissions -p / developer ".*" ".*" ".*"
   ```
   ![2023-07-23_072944.png](https://s2.loli.net/2023/07/23/QtNvsz1nfW6aVPC.png)

2. 查看(指定vhostpath)所有用户的权限

   ```shell
   rabbitmqctl  list_permissions
   ```

   ![2023-07-23_072820.png](https://s2.loli.net/2023/07/23/Ap27rHsjGg3JTYq.png)

   查看virtual host为/的所有用户权限

   ```shell
   rabbitmqctl  list_permissions -p /
   ```
   ![2023-07-23_073120.png](https://s2.loli.net/2023/07/23/Q1bDua9AlNk25Ci.png)

3. 查看指定用户的权限

   ```shell
   rabbitmqctl  list_user_permissions developer
   ```
   ![2023-07-23_073300.png](https://s2.loli.net/2023/07/23/AXtdCHklR8V1GEr.png)

4. 清除用户权限

   ```shell
   rabbitmqctl  clear_permissions developer
   ```
   ![2023-07-23_073404.png](https://s2.loli.net/2023/07/23/8X2uJSwUBET1Ay3.png)

参考 ： https://www.cnblogs.com/ericli-ericli/p/5902270.html

