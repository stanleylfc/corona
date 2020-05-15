+++
title="linux 服务实战（一）| Systemd"
tags=["linux"]
categories=["linux"]
date="2020-05-03T09:00:00+08:00"
toc=true
+++

## 1. 配置文件
### 1.1 查看配置文件
```
$ systemctl cat sshd.service

[Unit]
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.service
Wants=sshd-keygen.service

[Service]
EnvironmentFile=/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D $OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
Type=simple
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```
### 1.2 [Unit] 区块：启动顺序与依赖关系
- After字段：表示如果network.target或sshd-keygen.service需要启动，那么sshd.service应该在它们之后启动。
- Wants字段：表示sshd.service与sshd-keygen.service之间存在"弱依赖"关系，即如果"sshd-keygen.service"启动失败或停止运行，不影响sshd.service继续执行。
### 1.3 [Service] 区块：启动行为
-
- Type字段定义启动类型。它可以设置的值如下。
```
simple（默认值）：ExecStart字段启动的进程为主进程
forking：ExecStart字段将以fork()方式启动，此时父进程将会退出，子进程将成为主进程
oneshot：类似于simple，但只执行一次，Systemd 会等它执行完，才启动其他服务
dbus：类似于simple，但会等待 D-Bus 信号后启动
notify：类似于simple，启动结束后会发出通知信号，然后 Systemd 再启动其他服务
idle：类似于simple，但是要等到其他任务都执行完，才会启动该服务。一种使用场合是为让该服务的输出，不与其他服务的输出相混合
```
### 1.4 [Install] 区块
```
Target的含义是服务组，表示一组服务。WantedBy=multi-user.target指的是，sshd 所在的 Target 是multi-user.target。
```

## 2. 配置systemd服务
### 2.1 service文件拷贝到/lib/systemd/system
```
cp node_exporter.service /lib/systemd/system/
```
### 2.2 软连接应用
```
ln -s /opt/node_exporter/node_exporter /usr/local/bin/node_exporter
```