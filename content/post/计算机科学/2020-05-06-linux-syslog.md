+++
title="linux 实战（一）| syslog"
tags=["linux"]
categories=["linux"]
date="2020-04-03T09:00:00+08:00"
toc=true
+++

## 1. 安装
- 一般来说centos都自带了rsyslog,
- 通过源码包来安装
```
源码包下载地址:http://www.rsyslog.com/downloads/download-v8-stable/
```
- 使用yum
```
sudo yum install rsyslog
```

## 2. 开启我们的rsyslog远程服务
### 2.1 编辑配置文件
```
sudo vim /etc/rsyslog.conf

并将以下注释打开
# Provides UDP syslog reception
$ModLoad imudp #打开udp模块
$UDPServerRun 514 #打开udp服务，并监听514端口
# Provides TCP syslog reception
$ModLoad imtcp #打开tcp模块
$InputTCPServerRun 514 #打开tcp服务，并监听514端口

```
### 2.2 重启服务
```
sudo service rsyslog restart
```
## 3.使用golang 进行连接写入日志
- tail -f /var/log/syslog
```
    sysLog, err := syslog.Dial("tcp", "localhost:514",
        syslog.LOG_WARNING|syslog.LOG_DAEMON, "gameuser")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Fprintf(sysLog, "This is a daemon warning with demotag.")
    sysLog.Emerg("And this is a daemon emergency with demotag.")
```

## 