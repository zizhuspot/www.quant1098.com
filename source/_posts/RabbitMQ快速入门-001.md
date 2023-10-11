---
title: RabbitMQ Quickstart (I) --- installing RabbitMQ for docker
date: 2023-07-22 22:26:00
categories:
  - Infrastructure
tags: 
  - RabbitMQ
  - Message Queues
  - Getting Started
  - Event Driven
  - Basics
  - Middleware
  - docker
description: RabbitMQ is a popular open source messaging middleware that can be found in most distributed systems. It is reliable, flexible, and easy to use, making it an important choice for building asynchronous processing and event-driven architectures. The full text includes the following content a docker installation of RabbitMQ RabbitMQ user management, role management and permission settings three How to ensure that 99.99% of the message was sent successfully? How to ensure that 99.99% of messages are not lost through persistence? V How to ensure that 99.99% of messages in the queue are consumed?
cover: https://s2.loli.net/2023/07/24/1UNflTcoBgWEzC6.webp
---

# docker installation using RabbitMQ

#### i Install docker

1. Check the kernel 

    ```shell
    uname -r
    ``
2. If it is lower than 3.10, run the upgrade command

    ``shell
    yum update
    ```
3. Install the required packages. Execute the following command

    ```shell
    yum install -y yum-utils device-mapper-persistent-data lvm2
    ```
4. Set up the yum source (choose one), we recommend AliCloud, it's faster!

    ```shell
    yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    ```
5. Check all the docker versions in the repository, choose one and install it, if you don't want to choose one, just install it by default.

    ```shell
    yum list docker-ce --showduplicates | sort -r
    yum install docker-ce
    ```
6. Start docker and make it bootable.

    ```shell
    systemctl start docker
    systemctl enable docker
    ```
7. Check the docker version number

    ```shell
    docker version
    ```

#### II Installing RabbitMQ

1. Install rabbitmq using docker.

    Default installation username guest password guest

    ```shell
    docker run -dit --restart=always --name rabbitmq -p 15672:15672 -p 5672:5672 rabbitmq:3.9.2-management
    
    ``
    
    Installation with username and password
    
    ``python
    docker run -d --hostname my-rabbitmq --name rabbitmq -e RABBITMQ_DEFAULT_USER=username -e RABBITMQ_DEFAULT_PASS=password -p 15672:15672 -p 5672. 5672 rabbitmq:3.9.2-management
    
    -d: Run the container in the background.
    --name: Specify the container name.
    -p: Specify the port on which the service is running (5672: application access port; 15672: console web port number).
    -v: Map a directory or file.
    --hostname: hostname (an important caveat of RabbitMQ is that it stores data based on so-called "node names", which are hostnames by default).
    --e: Specify environment variables (RABBITMQ_DEFAULT_VHOST: default VM name; RABBITMQ_DEFAULT_USER: default username; RABBITMQ_DEFAULT_PASS: password for default username).
    ```
2. Autostart at boot

    ```shell
    docker update --restart=always rabbitmq
    docker container update --restart=always rabbitmq
    ```
3. Run the existing image

    ```shell
    docker start rabbitmq
    ```
4. Create a guest user by default.

    ```shell
    user: guest password: guest
5. rabbitmqctl user command usage

    When you use the rabbitmqctl user command you are prompted with the following:

    ```shell
    rabbitmqctl: command not found
    ```

    Then it is because rabbitmqctl is not soft-connected, and you need to go to rabbitmqctl's sbin directory to execute the rabbitmqctl command to be useful. Assuming I want to change the password for the admin user, I need to find the rabbitmqctl's sbin directory and switch to it in order to execute the command. If you don't know where the directory is, you can find it with the find command:

    ```shell
    find / -name "rabbitmqctl*"
    ```

    ![](https://s2.loli.net/2023/07/22/ouIjhcR5Ft8WJMd.png)

    Create a soft connection

    ```shell
   ln -s /var/lib/docker/overlay2/85480b72a306464a9b1c728d388ef6dca490ca2df29bee3b99c6634bb475dd96/diff/opt/rabbitmq/sbin/rabbitmqctl / usr/bin/rabbitmqctl

