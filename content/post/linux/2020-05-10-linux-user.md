+++
title="linux 基础实战（一）| 用户管理"
tags=["linux"]
categories=["linux"]
date="2020-05-10T09:00:00+08:00"
toc=true
+++

## 1.用户ssh 免密码失败
```sh
$ tail -10f  /var/log/secure
Authentication refused: bad ownership or modes for file /home/xxxxxx/.ssh/authorized_keys
#解决方法：chmod 600  /home/hadoop/.ssh/authorized_keys
```
