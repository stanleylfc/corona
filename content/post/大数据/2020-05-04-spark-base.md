+++
title="大数据 实战（五）| spark 原理"
tags=["spark"]
categories=["大数据"]
date="2020-05-04T21:00:00+08:00"
+++
## 1. spark是什么
- spark是一个快速，通用的海量数据处理框架，提供一种更优化的分布式计算框架
- spark 依赖hadoop的hdfs,以及分布式资源框架Yarn,mesos等

### 1.1 大规模数据处理的统一分析引擎
- spark是在Hadoop基础上的改进，是UC Berkeley AMP lab所开源的类Hadoop MapReduce的通用的并行计算框架，Spark基于map reduce算法实现的分布式计算，拥有Hadoop MapReduce所具有的优点；但不同于MapReduce的是Job中间输出和结果可以保存在内存中，从而不再需要读写HDFS，因此Spark能更好地适用于数据挖掘与机器学习等需要迭代的map reduce的算法
  	
- spark是基于内存计算框架，计算速度非常之快，但是它仅仅只是涉及到计算，并没有涉及到数据的存储，后期需要使用spark对接外部的数据源，比如hdfs


### 1.2 mapreduce的优化思路
### 1.3 

## 2. spark的四大特性
### 2.1 速度快
 spark 比 mapreduce快的2个主要原因
- 基于内存
- 进程和线程
### 2.2

## 3. spark 体系架构
### 3.1 spark context
### 3.2 cluster manager
### 3.3 worker node
### 3.4 Application
- 一个spark的应用程序，它是包含了客户端的代码和任务运行的资源信息

## 4. spark 核心RDD
  RDD(resilient distributed dataset) 是spark的核心概念，指的是一个只读的，可分区的分布式数据集（分布式弹性数据集），这个数据集的全部或部分可以缓存在内存，在多次计算间重用。Spark 的核心思路就是将数据集缓存在内存中加快读取速度，同时用RDD以较小的性能代价保证数据的鲁棒性。
### 4.1 RDD的创建
- 创建RDD 可以使用SparkContext对象
- 可以直接将集合序列化
- 最常用的是从文件创建
- SparkContext的两个方法： sc.parallelize([]) 和sc.textFile("/path/to/file")
### 4.2 RDD的转换操作
- 把一个RDD变成另外一个RDD
- 比如根据关键词过滤的filter操作