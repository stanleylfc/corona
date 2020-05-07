+++
title="vmware 实战（二）| linux基本命令"
tags=["vmware"]
categories=["vmware"]
date="2020-04-03T09:00:00+08:00"
toc=true
+++

## 1. useradd
```
// -M 没有home 目录
// -s /sbin/nologin without login shell
// -U create/adds a group with the same name as the user
useradd -M -U -s /sbin/nologin redis

```
## 