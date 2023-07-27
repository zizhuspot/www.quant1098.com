---
title: 在外面也能实现看家里收藏大片的神器-frp
date: 2023-07-26 21:41:00
categories:
  - 基础设施
tags:
  - frp
  - frpc
  - 域名解析
  - frps
  - 反向代理
  - 神器
  - 连接池
description: 'frp是一个高性能的反向代理服务器,可以帮助内网服务暴露到公网。通过在家中电脑上安装frp的客户端,在云服务器上安装服务端,就可以轻松访问家中的应用了。有了它,你的资源库随时随地都可以“指哪打哪”,再也不用担心外出时无法看片了。'
cover: https://s2.loli.net/2023/07/27/aBeRsLDK4JMcCQ2.png
---

我只能说，好艰难，我翻了半天都是只找到一个logo.ico图片文件，来代表这个项目，其余的地方真的找不到这个项目的标志，真的好难。 但是看代码却又在频繁更新。 只能说作者是一个理工科直男无疑了。

在这篇并不想搞成长篇大论的文章里，我也不太愿意从这个软件的意义、用途，技术架构，将来的发展演进讲起，因为在我看来，这些跟一般用户的距离相距太远。咱们不如直接讲干货，就是这个软件到底怎么用！

这篇文章分为几个部分

- 她解决了什么问题
- 不适用人群是那些
- 具体的安装和使用（有代码详细说明~）
- 总结

就可以了， 别的扯多了也没啥用

## 它解决了什么问题

我的生活，一直存在两堵墙，一个是伟大的长城墙，另一个是你想访问家里某个角落的重要文件，却发现自己在外面，无法访问。怎么才能访问到家里的大片，实现在外面也能看高清大片的愿望？

当然选frp啦~~ 最好用的穿透神器，没有之一！

当然， 同等类型的工具其实还有很多，为啥非要用frp?

### 其他工具比较

| 名称     | 特点                                        | 不足                                                         |
| -------- | ------------------------------------------- | ------------------------------------------------------------ |
| ngrok    | 通过在公网服务器上架设隧道,实现内网服务转发 | 需要登录账号,不够开源透明<br/>转发能力比较有限,不支持UDP<br/>性能相对较差,延迟高 |
| natapp   | 国内开发的内网穿透工具,采用C/S架构          | natapp的传输速度较慢<br>功能不算很强大,使用也不够灵活        |
| Zerotier | 点对点的内网穿透工具,通过创建虚拟网络来实现 | 网络环境适应性较差,容易被干扰<br>传输速度不如frp,延迟较高    |
| tunnelto | 支持 UDP 的国产内网穿透工具                 | 功能比较有限,配置也不够灵活<br>只支持 udp,不支持 tcp 和其他协议 |

综合来说，我站frp！

## 那些人不适用frp

总体来说,frp是一个非常优秀的内网穿透工具,可以满足大多数人的需求。但是,仍有一些场景下不太适合使用frp:

1. **非技术人员**
    frp需要进行一定的命令行操作和配置,这对不熟悉命令行的非技术人员来说,门槛会较高
2. **有严格网络安全规范的企业**
    frp可以使内网服务对外网开放,这对于有着严格安全规范和防火墙策略的企业来说,会存在一定安全风险。
3. **需要高度定制化的场景**
    frp更多是一种通用解决方案,如果场景需要高度定制化的内网穿透方案,frp就不太合适。
4. **在恶劣网络下使用**
    frp依赖公网服务器的网络质量,如果公网服务器网络条件差,Proxy连接容易断开。
5. **需要中继多级内网的场景**
    frp更适合两点直接穿透,如果内网跨多个局域网,frp的效果就会打折扣。
6. **有其他自研内网穿透解决方案的公司**
    如果公司已经投入自主研发了内网穿透产品,就没有必要再引入frp。
7. **注重传输速度的场景**
    frp相比商业化的内网穿透工具,传输速度上还有改进空间。
8. **使用Kubernetes等平台的环境**
    这类环境有更好的Service Mesh等网络管理方案,不需要单独使用frp。

所以对于以上人群或者场景,直接使用frp可能不是很合适,需要考虑其他方案或产品。但对于大多数个人用户和中小企业,frp仍然是一款非常有价值的开源项目。

## 具体的安装和使用

现在最新的版本是0.51.2，据说会进行大改版进化到1.x，让我们期待他的全新蜕变升级，安装使用目前来说，按照下面的步骤可以正常使用，愉快玩耍啦。

### 服务端

#### 下载

```bash
wget https://github.com/fatedier/frp/releases/download/v0.51.2/frp_0.51.2_linux_amd64.tar.gz
tar -xzf frp_0.51.2_linux_amd64.tar.gz
cd frp_0.51.2_linux_amd64
```

#### 配置frps的ini文件

```ini
[common]
bind_port = 7000
vhost_http_port = 8080
token = 123456
```

#### 启动 frps

```bash
./frps -c ./frps.ini
```

查看是否能够正式启动，如果启动成功，在 运行控制台里面不会返回任何消息，同时，访问服务器的7000端口会出现fprs的控制台信息面板，比如这样：

![](https://s2.loli.net/2023/07/27/em3W9aTPzNsFOwp.png)

![](https://s2.loli.net/2023/07/27/pKkgEutel7Oi9Sd.png)

#### 配置自启动

首先 `sudo vim /lib/systemd/system/frps.service` 在frps.service里写入以下内容

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

然后启动 frps `sudo systemctl start frps`
再打开自启动 `sudo systemctl enable frps` 就可以了，常用命令如下:

- 重启 `sudo systemctl restart frps`

- 停止 `sudo systemctl stop frps`

- 查看应用日志 `sudo systemctl status frps` 

客户端自启动配置类似。

### 本地端

#### 下载并安装

```bash
## 下载并解压
wget https://github.com/fatedier/frp/releases/download/v0.51.2/frp_0.51.2_linux_amd64.tar.gz
tar -xzf frp_0.51.2_linux_amd64.tar.gz
cd frp_0.51.2_linux_amd64
```

#### 修改 frpc.ini 文件

```ini
# 假设 frps 所在的服务器的 IP 为 x.x.x.x，local_port 为本地机器上 web 服务对应的端口, 绑定自定义域名 
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

#### 启动frpc

```bash
nuhup ./frpc -c ./frpc.ini &
```

#### 配置自启动

同fprs配置。

### 域名解析

将 `www.yourdomain.com` 的域名 A 记录解析到 IP `x.x.x.x`，如果服务器已经有对应的域名，也可以将 CNAME 记录解析到服务器原先的域名。

## 总结

frp是一个高性能的反向代理服务器,可以帮助内网服务暴露到公网。通过在家中电脑上安装frp的客户端,在云服务器上安装服务端,就可以轻松访问家中的应用了。

只需要简单的配置,frp就会建立内网服务器和外部连接的通道。你可以在外面访问家里电脑上的任何服务,比如远程桌面、网络共享、数据库服务等。 

突破了路由器防火墙的阻隔,你可以在任何地方使用手机或笔记本观看家中的视频、照片等数码收藏。无论是在公司加班,还是出差出游,都可以随时随地访问家里的资源,十分方便。

frp使用简单,客户端和服务端程序都很小巧,安装和配置很快就能完成。关键是frp的传输速度很快,画面和声音的流畅度不会有明显的延迟。即使在宽带条件不好的情况,也能保证浏览资源的流畅体验。

安全方面,frp也进行了充分的考虑,支持密码认证、TLS加密传输等多种安全策略,可以防止信息被他人获取。知道账号密码的才能访问你的内网资源。

总之,如果想在外面随时访问家中的懒人服务器,frp会是一个很好的选择。它可以说是实现跨网络泛在所欲为的“六神镇楼之宝”。有了它,你的资源库随时随地都可以“指哪打哪”,再也不用担心外出时无法看片了。

