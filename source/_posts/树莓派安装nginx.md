---
title: 树莓派安装nginx
date: 2023-08-05 15:19:00
categories:
  - 树莓派
tags:
  - 树莓派
  - nginx
  - php
  - Raspberry Pi
  - Web服务器
  - sqlite3
  - 配置文件
  - gogs
description: '安装各组件,并完成Nginx与PHP的集成,以及PHP对SQLite3的支持,做好这些配置后,就可以在树莓派上搭建PHP网站程序了'
cover: https://s2.loli.net/2023/08/05/bNxHYW5A4OPdeS2.png
---

## 将Nginx安装到Raspberry Pi上

```bash
sudo apt-get install nginx
```

### 在终端中输入以下命令来启动Raspberry Pi上的Web服务器。

```bash
sudo systemctl start nginx
```

### 访问树莓派IP地址:80验证是否安装成功

```bash
http://192.168.124.248:80/
```

## 添加php和sqlite3支持

### 添加php源

```bash
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```

### 启动sqlite3支持

```bash
sudo apt install php-sqlite3 sqlite3
```

### 安装php组件

```bash
sudo apt install php-redis
```

### 为PHP配置NGINX

```bash
sudo apt-get install php7.4-fpm php7.4-mbstring php7.4-mysql php7.4-curl php7.4-gd php7.4-curl php7.4-zip php7.4-xml -y
```

### 修改配置文件

1. ```bash
    sudo vim /etc/nginx/nginx.conf
    
    index index.html index.htm; 改为 
    index index.php index.html index.htm;
    ```
2. ```bash
    #location ~ \.php$ {
            #       include snippets/fastcgi-php.conf;
            #
            #       # With php5-cgi alone:
            #       fastcgi_pass 127.0.0.1:9000;
            #       # With php5-fpm:
            #       fastcgi_pass unix:/var/run/php5-fpm.sock;
            #} 改为
    ```

    ```bash
    location ~ \.php$ {
                   include snippets/fastcgi-php.conf;
                   fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
            }
    ```

### NGINX重新加载配置

```bash
sudo systemctl reload nginx
```

### 编辑index.php文件

```bash
 sudo vim /var/www/html/index.php 
 内容添加 一行代码 
 <?php phpinfo(); ?>
```

### 如果可以正常显示，就说明配置没问题了。

```bash
 在显示的内容中找到Loaded Configuration File的值，一般为`/etc/php/7.4/fpm/php.ini
 将extension=sqlite3一行取消注释,然后保存。
 # 重启fpm服务
 sudo service php7.4-fpm restart
```

‍
