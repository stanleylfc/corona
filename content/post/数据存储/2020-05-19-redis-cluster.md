+++
title="redis 实战（一）| redis 集群搭建"
tags=["redis"]
categories=["redis"]
date="2020-05-23T21:00:00+08:00"
+++




## 5.测试环境启动集群
- 启动服务
- 加入集群
```
$ redis-server /etc/redis/6378.conf
$ redis-server /etc/redis/6379.conf
$ redis-server /etc/redis/6380.conf
$ /usr/local/bin/redis-cli -a Q1fkAxyVq4cIC2Bb --cluster create --cluster-replicas 0 173.255.204.159:6379 173.255.204.159:6378 173.255.204.159:6380
```