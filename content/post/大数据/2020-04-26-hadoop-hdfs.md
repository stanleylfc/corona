+++
title="大数据 理论（一）| hadoop hdfs"
tags=["hadoop"]
categories=["大数据"]
date="2020-04-27T21:00:00+08:00"
+++

## 1. HDFS 
### 1.1 设计思路
- 顺序读而非随机读，Hadoop是用来做分析而不是事务性处理
- 硬件错误不可避免，因此要冗余
- 大数据集，因此需要平滑扩展
- 一次写入，多次读取。通常文件写入就不在变化（比如log)
- 就近读取原则分配数据节点

### 1.2 HDFS 架构


### 1.3 HDFS 角色
- Namenode (metadata)
- Secondary Namenode (CheckPoint namenode backup namenode) (用于合并元数据，备份元数据)
- Datanode (block, replication)
- client (write read)

