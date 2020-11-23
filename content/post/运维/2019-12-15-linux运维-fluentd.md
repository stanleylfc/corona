+++
title="Linux运维 实战（三）| fluentd"
tags=["Linux运维"]
categories=["Linux运维"]
date="2020-05-30T21:00:00+08:00"
+++

## 5.监听文件变化
### 5.1 基本命令
```
watch -n 1 -d 'du -sh /var/log/td-agent/task/taskbuffer/*'
```