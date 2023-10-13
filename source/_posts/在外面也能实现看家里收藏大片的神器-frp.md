---
title: The best way to watch your home collection outside - frp
date: 2023-07-26 21:41:00
categories:
  - Infrastructure
tags: 
  - frp
  - frpc
  - Domain Name Resolution
  - frps
  - reverse proxy
  - Magic Tools
  - Connection Pooling
description: frp is a high-performance reverse proxy server that helps expose intranet services to the public web. By installing frp client on your home computer and server on your cloud server, you can easily access your home applications. With it, your library can be accessed anywhere, anytime, and you no longer need to worry about not being able to watch movies when you're away from home.
cover: https://s2.loli.net/2023/07/27/aBeRsLDK4JMcCQ2.png
---

All I can say is that it's so tough, I've been digging around and I'm only finding a logo.ico image file to represent the project, and the rest of it is really hard to find the logo for this project. But looking at the code it is being updated frequently. Can only say that the author is a science and technology straight man undoubtedly.

In this article and do not want to get into a long-winded article, I am not too willing to start from the meaning of this software, the purpose, technical architecture, the future development of the evolution of speak, because in my opinion, these and the distance between the average user is too far. Let's get straight to the meat of the matter, which is how this software actually works!

This article is divided into several parts

- What problem she solves
- Who it doesn't apply to
- Installation and use of the specifics (with detailed instructions in code ~)
- Summary.

Just enough, nothing more than that is not useful.

## What problem it solves

In my life, there have always been two walls, one is the great wall of the Great Wall and the other is when you want to access an important file somewhere in your home but find yourself outside and unable to do so. How can you access the blockbusters in your home and realize your desire to watch HD blockbusters even outside?

Of course, frp is the best penetration tool out there!

Of course, there are a lot of other tools out there, why do you have to use frp?

### 其他工具比较

| 名称     | 特点                                                         | 不足                                                         |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ngrok    | Realize intranet service forwarding by tunneling on public servers. | Requires login account, not open source transparent<br/>Forwarding capacity is relatively limited, does not support UDP<br/>Performance is relatively poor, high latency |
| natapp   | Domestic development of intranet penetration tools, using C/S architecture | The transfer speed of natapp is slow<br/>Not very powerful and not flexible enough. |
| Zerotier | Peer-to-peer intranet penetration tool by creating virtual networks | Poor network environment adaptability, easy to be interfered with<br/>Transmission speed is not as good as frp, high latency |
| tunnelto | Domestic Intranet Penetration Tool with UDP Support          | Limited functionality, configuration is not flexible enough<br/>Only support udp, does not support tcp and other protocols |

On balance, I stand frp!

## Those who do not apply frp

Overall, frp is a very good tool for penetrating intranets, and can fulfill most people's needs. However, there are still some scenarios where frp is not suitable.

1. **non-technical personnel***
   frp need to carry out certain command-line operations and configuration, which is not familiar with the command line for non-technical personnel, the threshold will be higher
2. **Enterprises with stringent network security norms**.
   frp can make the intranet services open to the extranet, which has a strict security norms and firewall policy for the enterprise, there will be a certain security risk.
3. **The need for highly customized scenarios
   frp is more of a general solution, if the scene requires a highly customized intranet penetration solution, frp is not suitable.
4. **Used under poor network conditions
   frp relies on the network quality of the public server, if the public server network conditions are poor, the Proxy connection is easy to disconnect.
5. **The need to relay multi-level intranet scenarios
   frp is more suitable for two-point direct penetration, if the intranet across multiple LANs, the effect of frp will be compromised.
6. **Companies that have other self-developed intranet penetration solutions
   If the company has already invested in independent research and development of intranet penetration products, there is no need to introduce frp.
7. **Scenarios that emphasize on transmission speeds
   Compared with commercialized intranet penetration tools, frp still has room for improvement in terms of transmission speed.
8. **Using Kubernetes and other platform environments** **This type of environment has a better Service Mode than the commercialized intranet penetration tool.
   This type of environment has better Service Mesh and other network management programs, do not need to use frp separately.

So for the above crowd or scene, direct use of frp may not be very suitable, need to consider other programs or products. But for most individual users and small and medium-sized , frp is still a very valuable open source project .

## Specific installation and use

Now the latest version is 0.51.2, it is said that will be a major revamp evolution to 1.x, let us look forward to his new transformation upgrade, installation and use of the current, in accordance with the following steps can be used normally, happy to play it.

### Server

#### Download

```bash
wget https://github.com/fatedier/frp/releases/download/v0.51.2/frp_0.51.2_linux_amd64.tar.gz
tar -xzf frp_0.51.2_linux_amd64.tar.gz
cd frp_0.51.2_linux_amd64
```

#### Configuring the frps ini file

```ini
[common]
bind_port = 7000
vhost_http_port = 8080
token = 123456
```

#### Start frps

```bash
./frps -c ./frps.ini
```

To see if it can be officially started, if it is successful, no message will be returned in the runtime console, and the fprs console message panel will appear when accessing port 7000 of the server, like this:

![](https://s2.loli.net/2023/07/27/em3W9aTPzNsFOwp.png)

![](https://s2.loli.net/2023/07/27/pKkgEutel7Oi9Sd.png)

#### Configuring Self-Start

First `sudo vim /lib/systemd/system/frps.service` Write the following in frps.service

```ini
[Unit]
Description=frps service
After=network.target network-online.target syslog.target
Wants=network.target network-online.target

[Service]
Type=simple

#启动服务的命令（此处写你的frps的实际安装目录）
ExecStart=/your/path/frps -c /your/path/frps.ini

[Install]
WantedBy=multi-user.target
```

Then start frps `sudo systemctl start frps`.
Then turn on the autostart `sudo systemctl enable frps` and that's it, the common commands are as follows.

- restart `sudo systemctl restart frps`

- Stop `sudo systemctl stop frps`.

- Check the application log `sudo systemctl status frps`. 

Client self-start configuration is similar.

### Local

#### Download and install

```bash
## Download and unzip
wget https://github.com/fatedier/frp/releases/download/v0.51.2/frp_0.51.2_linux_amd64.tar.gz
tar -xzf frp_0.51.2_linux_amd64.tar.gz
cd frp_0.51.2_linux_amd64
```

#### Modify the frpc.ini file

```ini
# Assuming that frps is hosted on a server with an IP of x.x.x.x, and local_port is the port of the web service on the local machine, bind the custom domain name 
[common]
server_addr     = 'x.x.x.x' 
server_port     = 7000 


[DSM, Download Station, Audio Station, Video Station]
type            = tcp 
local_ip        = 127.0.0.1 
local_port      = 5000 
remote_port     = 5000

[ssh1]
type = tcp
local_ip = 172.16.3.52
local_port = 22
remote_port = 6100

[web11]
type = http
local_port = 9007
custom_domains = www.yourdomain.com
locations = /bbsp
```

#### Starting frpc

```bash
nuhup ./frpc -c ./frpc.ini &
```

#### Configuring Self-Start

Same as fprs configuration.

### Domain name resolution

Resolve `www.yourdomain.com` domain A records to IP `x.x.x.x`, or CNAME records to the server's original domain name if the server already has a corresponding domain name.

## Summary

frp is a high-performance reverse proxy server that can help expose intranet services to the public network. By installing the frp client on your home computer and the server on the cloud server, you can easily access your home applications.

With a simple configuration, frp establishes a channel between the intranet server and the external connection. You can access any service on your home computer from outside, such as remote desktop, network sharing, database service, etc. 

Breaking through the router's firewall, you can use your cell phone or laptop to watch your home videos, photos and other digital collections from anywhere. Whether you are working overtime in the company, or traveling on business, you can access your home resources anytime, anywhere, which is very convenient.

frp is easy to use, client and server programs are very small, installation and configuration can be completed quickly. The key is that the transmission speed of frp is very fast, the smoothness of the screen and sound will not have a significant delay. Even in the case of broadband conditions are not good, but also to ensure the smooth experience of browsing resources.

Security, frp also made full consideration, support password authentication, TLS encrypted transmission and other security policies, you can prevent information from being accessed by others. Only those who know the account password can access your intranet resources.

In short, if you want to access the home at any time outside the lazy server, frp will be a good choice. It can be said to be the realization of cross-network ubiquitous all you want to do "six God's treasure of the town". With it, your library of resources at any time and anywhere can be "wherever you want to play", no longer have to worry about not being able to watch movies when you go out.

