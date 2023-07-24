---
title: RabbitMQ快速入门(一) --- docker 安装使用 RabbitMQ
date: 2023-07-22 22:26:00
categories:
  - 基础设施
tags:
  - RabbitMQ
  - 消息队列
  - 入门
  - 事件驱动
  - 基础知识
  - 中间件
  - docker
description: 'RabbitMQ是一个非常流行的开源消息中间件,可以在大多数分布式系统中找到它的身影。它具有可靠、灵活、易用等优点,是构建异步处理和事件驱动架构的重要选择。 全文包括以下内容一docker安装RabbitMQ 二 RabbitMQ用户管理,角色管理及权限设置 三 如何保证消息99.99%被发送成功？四 如何通过持久化保证消息99.99%不丢失? 五 如何保证队列里的消息99.99%被消费'
cover: https://s2.loli.net/2023/07/24/1UNflTcoBgWEzC6.webp
---

# docker 安装使用 RabbitMQ

#### 一 安装docker

1. 检查内核 

    ```shell
    uname -r
    ```
2. 如果低于3.10，执行升级命令

    ```shell
    yum update
    ```
3. 安装需要的软件包。执行下面命令

    ```shell
    yum install -y yum-utils device-mapper-persistent-data lvm2
    ```
4. 设置yum源（选择其中一个），推荐阿里云，速度快一点

    ```shell
    yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    ```
5. 查看仓库中所有docker版本，选择一个安装，懒得选就直接默认安装就好

    ```shell
    yum list docker-ce --showduplicates | sort -r
    yum install docker-ce
    ```
6. 启动docker，设为开机自启动

    ```shell
    systemctl start docker
    systemctl enable  docker
    ```
7. 查看docker版本号

    ```shell
    docker version
    ```

#### 二 安装RabbitMQ

1. 使用docker 安装rabbitmq

    默认安装 用户名 guest 密码 guest

    ```shell
    docker run -dit --restart=always --name rabbitmq -p 15672:15672 -p 5672:5672 rabbitmq:3.9.2-management
    
    ```

    带有用户名密码的安装

    ```python
    docker run -d --hostname my-rabbitmq --name rabbitmq -e RABBITMQ_DEFAULT_USER=username -e RABBITMQ_DEFAULT_PASS=password -p 15672:15672 -p 5672:5672 rabbitmq:3.9.2-management
    
    -d：后台运行容器。
    --name：指定容器名。
    -p：指定服务运行的端口（5672：应用访问端口；15672：控制台Web端口号）。
    -v：映射目录或文件。
    --hostname：主机名（RabbitMQ的一个重要注意事项是它根据所谓的“节点名称”存储数据，默认为主机名）。
    -e：指定环境变量（RABBITMQ_DEFAULT_VHOST：默认虚拟机名；RABBITMQ_DEFAULT_USER：默认的用户名；RABBITMQ_DEFAULT_PASS：默认用户名的密码）。
    ```
2. 开机自动启动

    ```shell
    docker update --restart=always rabbitmq
    docker container update --restart=always rabbitmq
    ```
3. 运行已有镜像

    ```shell
    docker start  rabbitmq
    ```
4. 默认创建了一个guest用户

    ```shell
    user: guest password: guest
    ```
5. rabbitmqctl用户命令使用

    当你使用rabbitmqctl用户命令时有如下提示：

    ```shell
    rabbitmqctl: command not found
    ```

    则是因为rabbitmqctl没有进行软连接，需要进入到rabbitmqctl的sbin目录下执行rabbitmqctl命令才有用。假设我要修改admin用户密码，需要找到rabbitmqctl的sbin目录并切换才能执行命令，如果不清楚目录在哪里，可以通过find命令查找：

    ```shell
    find / -name "rabbitmqctl*"
    ```

    ![](https://s2.loli.net/2023/07/22/ouIjhcR5Ft8WJMd.png)

    创建软连接

    ```shell
   ln -s /var/lib/docker/overlay2/85480b72a306464a9b1c728d388ef6dca490ca2df29bee3b99c6634bb475dd96/diff/opt/rabbitmq/sbin/rabbitmqctl /usr/bin/rabbitmqctl

