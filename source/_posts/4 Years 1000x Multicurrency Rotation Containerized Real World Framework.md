---
title: 4 Years 1000x Multicurrency Rotation Containerized Real World Framework
date: 2023-10-15 13:07:00
categories:
  - quantitative Trading
tags:
  - Digital currency
  - container
  - Quantitative tool
  - Kuangbiao
  - Multicurrency
  - quant1098.com
  - 1000x
  - Rotation
description: And with the container, you can completely copy the boss (clone) my deployment environment, all you have to do is with their own API key, knock a line of code, and then the money machine up, happy?
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

# 4 Years 1000x Multicurrency Rotation Containerized Real World Framework

```python
# rotation strategy
def signal_rotatory(df, n=480):
    """
    Rotation strategy: choose the coin with the biggest increase in n days
    """
    df['raw_rotatory'] = df['close'].pct_change(n)*(-1)
    df.loc[df['raw_rotatory'] < 0, 'rotatory'] = df['raw_rotatory']
    df.drop(['raw_rotatory'], axis=1, inplace=True)
    return df
```

## Why Containerized Deployment

Containers is the most popular technology of the current Internet technology, based on containers will be isolated from environmental resources to achieve rapid deployment of operations and maintenance. Simply put, I have published more live code, but after each announcement, there is always the boss because the python version is inconsistent, less installed a dependency package, package version is inconsistent or operating system differences that lead to strategy anomalies, such as the strategy does not run up, run up and inconsistent with the expected results.

And with the container, you can completely copy the boss (clone) my deployment environment, all you have to do is with their own API key, knock a line of code, and then the money machine up, happy?

## 30s Rapid Containerized Deployment

### 1 Code for uploading attachments

Upload the code to the server, here you can use whatever transfer tool you want, no special requirements.

### 2 Install the docker environment

```python
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

If you are using centos, you need to run one more line.  
Start the docker daemon process ` sudo systemctl start docker`  
Keep docker on boot ` sudo systemctl enable docker`

### 3 Modifying configuration files

The configuration file is in the .env file, where you can configure the API and exception alert robot. Note that here .env is a hidden file, Linux/Mac ls -a can be viewed, windows system set to show hidden files can be.

```python
DINGDING_ROBOT_ID =  ''
DINGDING_SECRET = ''
TELEGRAM_TOKEN = '1908201261:AAEND2Cex_SUaTNbokx3AQ'
TELEGRAM_CHAT_ID = -590128321
WECHAT_CORPID = ''
WECHAT_SECRET = ''
WECHAT_AGENT_ID = ''

```

Alert bots are assigned to whichever bot the owner uses, and the program detects this automatically. If they are all assigned, the bots are actually instantiated according to the priority of dingding>tg>wechat.

![01](assets/01-20230814202341-1y7rwby.png)​

The strategy's leverage configuration defaults to 2x leverage.

![02](assets/02-20230814202353-aw6wbn6.png)​

### 4 Containerized Build Deployment

#### Switch to the project directory

`cd MultiCoinRotation`

#### Compile build image multicoinrotation:1.0.0 with image name and version number

`docker build -t multicoinrotation:1.0.0 . `

#### start the container -d run it in background, --name specify the container name

`docker run -it -d --name=multicoinrotation multicoinrotation:1.0.0`

# Live Disk Operations

This can be accomplished with the help of common docker commands, a few of which are listed below.

```python
docker ps # view running containers
docker stop container name/container id # stop the container
docker start container name/container id # Start the container
docker restart container name/container id.
```

View the log of the most used, here are the details.

```python
$ docker logs [OPTIONS] CONTAINER
  Options.
        --details Show more information
    -f, --follow Track live logs
        --since string Show logs since a certain timestamp, or relative time, e.g. 42m (i.e. 42 minutes).
        --tail string Show how many lines from the end of the log, the default is all.
    -t, --timestamps Show timestamps.
        --until string Show logs from before a certain timestamp, or relative time, e.g. 42m (i.e. 42 minutes).
```

View the log after a specified time, showing only the last 100 lines:

`$ docker logs -f -t --since="2018-02-08" --tail=100 CONTAINER_ID`​

View the last 30 minutes of logs for.

`$ docker logs --since 30m CONTAINER_ID`​

![03](assets/03-20230814202412-vkba8oi.png)​

View logs after a certain time:

`$ docker logs -t --since="2018-02-08T13:23:37" CONTAINER_ID`​

View logs for a certain time period:

`$ docker logs -t --since="2018-02-08T13:23:37" --until "2018-02-09T12:23:37" CONTAINER_ID`​



attachment：

 https://github.com/zqwuming/blogimage/blob/main/asset/MultiCoinRotation-main.zip

‍
