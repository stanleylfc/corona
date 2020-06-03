+++
title="redis 实战（一）| redis 安装"
tags=["redis"]
categories=["redis"]
date="2019-05-23T21:00:00+08:00"
+++

## 1. redis 特征
### 1.1 特性
- 内存
- K/V
- 类型
- 本地方法
- 计算向数据移动
### 1.2 redis worker
- 单线程计算
- 计算串行化（原子）
- 网卡流量（瓶颈
- io thread 多线程(单个连接是有顺序， 多个客户端不确保顺序)
### 1.3 秒杀
- 客户端并发连接数 
- 服务器执行并行度
- redis 串行化

## 2. 5种Value 类型
### 2.1 string
#### 2.1.1 字符串
- session
- uuid
- VFS in mem 小文件
#### 2.1.2 数值
- 限流器
- 点击率
- 统计
#### 2.1.3 bitmap


- list
- hash
- set
- zset