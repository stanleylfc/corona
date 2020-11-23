+++
title="大数据 理论（一）| hadoop 原理"
tags=["hadoop"]
categories=["大数据"]
date="2020-04-27T21:00:00+08:00"
+++

## 2. HDFS 
### 2.1 设计思路
- 顺序读而非随机读，Hadoop是用来做分析而不是事务性处理
- 硬件错误不可避免，因此要冗余
- 大数据集，因此需要平滑扩展
- 一次写入，多次读取。通常文件写入就不在变化（比如log)
- 就近读取原则分配数据节点

### 2.2 HDFS 架构
- Namenode (metadata)
- Datanode (block, replication)
- client (write read)
- Secondary Namenode (CheckPoint namenode backup namenode) (用于合并元数据，备份元数据)
### 2.3 完全分布式安装步骤
- 参考hadoop 安装

## 3.MapReduce 原理
- inputFormat
- shuffle & sort 
- OutputFormat

## 4.Yarn 架构
- client
- Resource Manager
- Node Manager ((app Mstr) | Container)

## 