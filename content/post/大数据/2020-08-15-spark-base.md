+++
title="大数据 实战（五）| spark 原理"
tags=["spark"]
categories=["大数据"]
date="2020-05-02T21:00:00+08:00"
+++
## 1. spark是什么
- 大规模数据处理的统一分析引擎
```
1. spark是在Hadoop基础上的改进，是UC Berkeley AMP lab所开源的类Hadoop MapReduce的通用的并行计算框架，Spark基于map reduce算法实现的分布式计算，拥有Hadoop MapReduce所具有的优点；但不同于MapReduce的是Job中间输出和结果可以保存在内存中，从而不再需要读写HDFS，因此Spark能更好地适用于数据挖掘与机器学习等需要迭代的map reduce的算法。
  	
2. spark是基于内存计算框架，计算速度非常之快，但是它仅仅只是涉及到计算，并没有涉及到数据的存储，后期需要使用spark对接外部的数据源，比如hdfs。
```

## 2. spark的四大特性
### 2.1 速度快
#### 2.2 spark 比 mapreduce快的2个主要原因
```
1. 基于内存
2. 进程和线程
```
## 3. spark  集群架构
### 3.1 spark context
### 3.2 cluster manager
### 3.3 worker node
### 3.4 Application
- 一个spark的应用程序，它是包含了客户端的代码和任务运行的资源信息
