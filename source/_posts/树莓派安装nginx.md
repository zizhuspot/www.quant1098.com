---
title: Raspberry Pi Installation of nginx
date: 2023-08-05 15:19:00
categories: 
  - Raspberry Pi
tags: 
  - Raspberry Pi
  - nginx
  - sqlite3
  - Configuration files
  - gogs
description: Install the components, and complete the integration of Nginx and PHP, as well as PHP support for SQLite3, after these configurations, you can build PHP web programs on the Raspberry Pi 
cover: https://s2.loli.net/2023/08/05/bNxHYW5A4OPdeS2.png
---

## Installing Nginx on a Raspberry Pi

```bash
sudo apt-get install nginx
```

### Enter the following command in the terminal to start the web server on the Raspberry Pi.

```bash
sudo systemctl start nginx
```

### Access Raspberry Pi IP address:80 to verify successful installation

```bash
http://192.168.124.248:80/
```

## Add php and sqlite3 support

### Add php source

```bash
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```

### Start sqlite3 support

```bash
sudo apt install php-sqlite3 sqlite3
```

### Installing php components

```bash
sudo apt install php-redis
```

### Configuring NGINX for PHP

```bash
sudo apt-get install php7.4-fpm php7.4-mbstring php7.4-mysql php7.4-curl php7.4-gd php7.4-curl php7.4-zip php7.4-xml -y
```

### Modify the configuration file

1. ```bash
    sudo vim /etc/nginx/nginx.conf
    
    index index.html index.htm; change into 
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
            #} change into 
    ```

    ```bash
    location ~ \.php$ {
                   include snippets/fastcgi-php.conf;
                   fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
            }
    ```

### NGINX reloading configuration

```bash
sudo systemctl reload nginx
```

### Edit index.php file

```bash
 sudo vim /var/www/html/index.php 
 Content added One line of code 
 <?php phpinfo(); ?>
```

### If it can be displayed properly, the configuration is fine.

```bash
 Find the value of Loaded Configuration File in the display, usually `/etc/php/7.4/fpm/php.ini'.
 Uncomment the extension=sqlite3 line and save.
 # Restart the fpm service.
 sudo service php7.4-fpm restart
```

‍
