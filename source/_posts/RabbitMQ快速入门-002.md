---
title: RabbitMQ Quick Start (II) --- II RabbitMQ User Management, Role Management and Permission Setting
date: 2023-07-23 08:26:00
categories:
  - Infrastructure
tags: 
  - RabbitMQ
  - Message Queues
  - Getting Started
  - User Management
  - Basics
  - Role Management
  - Privilege settings
description: RabbitMQ is a very popular open source messaging middleware , you can find it in most distributed systems . It is reliable, flexible, easy to use and other advantages, is an important choice for building asynchronous processing and event-driven architecture. The full text includes the following content a docker installation of RabbitMQ RabbitMQ user management, role management and permission settings three How to ensure that 99.99% of the message was sent successfully? How to ensure that 99.99% of messages are not lost through persistence? V How to ensure that 99.99% of messages in the queue are consumed?
cover: https://s2.loli.net/2023/07/24/1UNflTcoBgWEzC6.webp
---

## II RabbitMQ User Management, Role Management and Permission Setting

Because RabbitMQ is installed using docker, you need to enter RabbitMQ in the docker environment first.

```shell
# Enter the docker environment
sudo docker exec -it [container name ps.rabbitmq] /bin/bash
# Check the status
rabbitmqctl status
```

![2023-07-23_070848.png](https://s2.loli.net/2023/07/23/dHYSOjvsRTq5faM.png)

#### 一 user management

1. ##### View User List

   ```shell
   rabbitmqctl list_users
   ```

   ![2023-07-23_071011.png](https://s2.loli.net/2023/07/23/h1J4agofqscKIPH.png)

2. ##### New User

   ```shell
   rabbitmqctl add_user developer 456789
   ```

   ![2023-07-23_071316.png](https://s2.loli.net/2023/07/23/3gVyCc76uRDOT8s.png)

3. ##### delete user

   ```shell
   rabbitmqctl delete_user developer
   ```

   ![2023-07-23_071444.png](https://s2.loli.net/2023/07/23/s6FTgW58u7jAHPx.png)

4. ##### chagne password

   ```shell
   rabbitmqctl change_password developer developer123456
   ```

   ![2023-07-23_071701.png](https://s2.loli.net/2023/07/23/qz1QIem2J4Avyjp.png)

‍

#### 二 characterization

1. In RabbitMQ, there are several roles: administrator, monitoring, policymaker, management, impersonator, and none.

   The default user guest is in the administrator role, and the new developer user has no role set, i.e., none, if we want to set the developer user to the administrator role

1. 

   ```shell
   rabbitmqctl set_user_tags developer administrator
   ```
   
   ![2023-07-23_071855.png](https://s2.loli.net/2023/07/23/sovTtn8EAhJc1ZH.png)

   It is also possible to set multiple roles for a user, such as administrator, monitoring for the user developer:

   ```shell
   rabbitmqctl set_user_tags developer administrator monitoring
   ```
   ![2023-07-23_072002.png](https://s2.loli.net/2023/07/23/sAXQuOeCZln73tV.png)



#### III Privilege Configuration

Now this developer account doesn't have the privileges to manage configure, write, and read, so we need to add them.

![](https://s2.loli.net/2023/07/23/RTLsSZYNmoiXEnD.png)

1. Setting Up Permissions
   ```shell
   rabbitmqctl set_permissions -p / developer ".*" ".*" ".*"
   ```
   ![](https://s2.loli.net/2023/07/23/QtNvsz1nfW6aVPC.png)
   
2. View the permissions of all users on the specified vhostpath.

   ```shell
   rabbitmqctl  list_permissions
   ```

   ![](https://s2.loli.net/2023/07/23/Ap27rHsjGg3JTYq.png)

   View all user permissions for virtual host as /

   ```shell
   rabbitmqctl  list_permissions -p /
   ```
   ![](https://s2.loli.net/2023/07/23/Q1bDua9AlNk25Ci.png)

3. To view the privileges of a specified user

   ```shell
   rabbitmqctl  list_user_permissions developer
   ```
   ![2023-07-23_073300.png](https://s2.loli.net/2023/07/23/AXtdCHklR8V1GEr.png)

4. Clearing User Privileges

   ```shell
   rabbitmqctl  clear_permissions developer
   ```
   ![2023-07-23_073404.png](https://s2.loli.net/2023/07/23/8X2uJSwUBET1Ay3.png)

consultation ： https://www.cnblogs.com/ericli-ericli/p/5902270.html

