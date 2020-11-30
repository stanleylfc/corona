+++
title="大数据 理论（二）| zookeeper 原理"
tags=["zookeeper"]
categories=["大数据"]
date="2020-04-28T21:00:00+08:00"
+++
## 1. zookeeper
### 1.1 什么是zookeeper 
- 分布式协调服务
- 配置维护
- 命名服务
- 分布式锁服务

### 1.2 zookeeper 结构
- Zookeeper Service
- Server(Follower Leader)
- Client

### 1.3 为什么使用Zookeeper
- 分布式应用需要一个主控、协调器或者控制器来管理物理分布的子进程
- 大部分应用需要开发私有的协调程序，缺乏一个通用的机制
- 协调程序的反复编写浪费，且难以形成通用、伸缩性好的协调器
- zookeeper:提供通用的分布式锁服务，用以协调分布式应用

### 1.4 Zookeeper 能做什么
- 集群管理
- 分布式同步
- 配置管理

### 1.7 Zookeeper 的角色
- 领导者(leader)， 负责进行投票的发起和决议，更新系统状态
- 学习者（learner) ，包括跟随者(follower)和观察者(observer)
- follower 用于接受客户端请求并想客户端返回结果，选主过程中参与投票
- observer 可以接受客户端连接，将写请求转发给leader，但是observer不参与投票过程,只同步leader的状态，observer的目的是为了扩展系统，提高读取速度
- 客户端，请求发起方

### 1.8 Zookeeper 的节点
- Znode 有两种类型：临时的和持久的，这两个类型又各自有自增序列和非自增序列
- Znode 的类型在创建时确定并且之后不能再修改
- 临时znode的客户端会话结束时，zookeeper会将该临时znode删除，临时znode不可以有子节点
- 持久znode 不依赖于客户端会话，只有当客户端明确要删除该持久znode时才会被删除

### 1.9 Zookeeper 的读写机制
- Zookeeper 是一个由多个server 组成的集群
- 一个leader,多个follower
- 每个server 保存一份数据副本
- 全局数据一致
- 分布式读写
- follower 转发更新请求，由leader实施

### 1.10 Zookeeper 写数据流程
- Client 向zk的server1上写数据，发送一个写的请求
- 如果server1 不是leader, 那么server1 会把接收的请求转发给leader。这个leader会将写请求广播各个server,各个server写成功后会通知leader
- 当leader 收到大多数server(超半数)写成功的消息，那么就说明数据写成功了。
- server1 会通知Client数据写成功了

### 1.11 Zookeeper 更新数据的保证
- 更新请求顺序进行，来自同一个client的更新请求按其发送顺序依次执行
- 数据更新原子性，一次数据更新要么成功，要么失败
- 全局唯一数据视图，client无论链接到那个server， 数据视图都是一致的
- 实施性，在一定事件范围内，client能读到最新数据

### 1.12 Zookeeper 读数据流程

## 2. 监视器 （watcher)
- Watcher 可以监控目录节点的数据变化以及子目录的变化，一旦状态发生变化，服务器就会通知所有设置在这个目录节点上的Watcher,从而每个客户端所关注的目录节点的状态发生变化
- 可以设置观察的操作  exists getChildren getData
- 可以触发观察的操作  create delete setData
### 2.1 写操作与zookeeper 内部事件的对应关系
create("/path") ==> Event For "/path" (EventType.NodeCreated)
### 2.2 Zookeeper 内部事件与watcher的对应关系
Event For "/path"(EventType.NodeCreated) ==>  exists("/path")  | getData("/path")

## 3. 鉴权ACL
- digest: 用户名 密码
- host: 通过客户端主机名来识别客户端
- ip: 通过客户端ip来识别客户端

## 4.Zookeeper 工作原理
- Zookeeper 的核心是原子广播，这个机制保证了各个server之间的同步。实现这个机制的协议叫做ZAB协议
- ZAB协议有两种模式：恢复模式，广播模式
- 当服务启动或者在领导者崩溃后，Zab就进入恢复模式，当领导者被选举出来，且大多数server完成了和leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和server具有相同的系统状态
- 一旦leader已经和多数follower进行了状态同步后，他就可以开始广播消息了，即进入了广播状态。这时候当一个server加入zookeeper服务中，它会在恢复模式下启动，发现leader，并和leader进行状态同步。待到同步结束，它也参与消息广播。Zookeeper服务一直维持在Broadcast状态，直到leader崩溃或者leader失去大部分的followers支持
- 广播模式需要保证proposal被按顺序处理，因此zk采用了递增的事务id号（zxid)来保证。实现zxid是一个64位的数字，高32位标识leader是否改变，低32位是个递增计数。

## 5. leader 选举
- 工作状态
- 
```

```

## 6. 应用场景
## 6.1 统一命名服务
- 分布式应用中，需要一套完整的命名规则，既能够产生唯一的名称又便于人识别和记住
- Name Service 是Zookeeper内置的功能
### 6.2 配置管理
- 配置信息保存在Zookeeper的某个目录节点中，然后将所有需要修改的应用及其监控配置信息的状态，一旦配置信息发生变化，每台应用机器就会收到
Zookeeper 的通知，然后从Zookeeper 获取新的配置信息应用到系统中
### 6.3 集群管理
- zookeeper 不仅能够维护当前集群中机器的服务状态，而且能够选出一个总管
- 规定编号最小的为master,所以当我们对servers节点做监控，得到服务器列表，只要所有集群机器逻辑认为最小编号节点为master，
那么master就被选出。这个master宕机，相应的znode会消失，然后新的服务器列表就被推送到客户端，然后每个节点逻辑认为最小编号节点
为master，这样就做到动态master选举。
### 6.4 共享锁
### 6.5 互斥锁
### 6.6 队列管理

## 7 总结
- ZK  提供了一套很好的分布式集群管理机制，就是它这种基于层次的目录树的数据结构，并对树的节点进行有效管理，从而可以设计出多种多样的分布式的数据管理模型