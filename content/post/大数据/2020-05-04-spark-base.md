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


### 1.2 mapreduce 特点
- 优势： 分布式，容错，扩展，编程模型简单
- 不足： 数据反复存盘，不适合迭代计算以及交互性强的需求

### 1.3 mapreduce的优化思路
- 非循环数据模型：数据单向流动，无法复用
- 循环数据模型：数据循环迭代使用
- 传统的内存-硬盘模式：数据IO成本高
- 弹性分布式数据集：利用内存，提升整体效率

## 2. spark的四大特性
### 2.1 速度快
 spark 比 mapreduce快的2个主要原因
- 基于内存
- 进程和线程

## 3. spark 体系架构
- spark context
- cluster manager
- worker node
- Application
- 一个spark的应用程序，它是包含了客户端的代码和任务运行的资源信息

## 4. spark 核心RDD
  RDD(resilient distributed dataset) 是spark的核心概念，指的是一个只读的，可分区的分布式数据集（分布式弹性数据集），这个数据集的全部或部分可以缓存在内存，在多次计算间重用。Spark 的核心思路就是将数据集缓存在内存中加快读取速度，同时用RDD以较小的性能代价保证数据的鲁棒性。
### 4.1 RDD的创建
- 创建RDD 可以使用SparkContext对象
- 可以直接将集合序列化
- 最常用的是从文件创建
- SparkContext的两个方法： 
```
sc.parallelize([]) 
sc.textFile("/path/to/file")
```

### 4.2 RDD的转换操作
- 把一个RDD变成另外一个RDD
- 比如根据关键词过滤的filter操作
- 对{1,2,3,4}的RDD进行行动操作
|函数名|目的|示例|结果|
|--|--|--|--|--|
|map()| 应用与RDD的每一个元素，将返回值映射为新的RDD| rdd.map(x=>x+1) | {2,3,4,5}|
|flatmap()| 输入是RDD的每一元素，输出是迭代器| rdd.flatmap(x=>fact(x))|{1,2,3,2,2}|
|filter()| 过滤，形成新的RDD|rdd.filter(x=>x!=1)| {2,3,4}|
|distinct()| 去重|rdd.distinct()| {1,23,4}|
|union()| 连接两个RDD|rdd.union({5,6})|{1,2,3,4,5,6}|

### 4.3 RDD的行动操作
- 行动操作可以理解为对RDD执行相应的计算
- 对{1,2,3,4}的RDD进行行动操作
|函数名|目的|示例|结果|
|--|--|--|--|--|
|collect()| 返回RDD的所有元素| rdd.collect() | {1,2,3,4}|
|count()| RDD的元素个数| rdd.count()|4|
|taken(n)| 从RDD中返回n个元素|rdd.take(2)| {1,2}|
|top(n)| 返回最前面的n个元素|rdd.top(2)| {3,4}|
|reduce(func)| 整合rdd的所有数据执行func操作|rdd.reduct((x,y)=>x+y)|10|

### 4.4 RDD 关键概念--惰性求值
- 所有的RDD 转换操作都是惰性求值，在行动操作之前spark不会开始任何计算
```
$ pyspark
>>> sc
>>> inputRDD = sc.textFile("pyspark")
>>> pyRDD=inputRDDfilter(lambda x: "Python" in x)
>>> pyRDD.count()
>>> pyRDD.top(3)
>>> pyRDD.first(1)  
```
## 5 spark的部署方式
- 存储用HDFS
- 资源调度用yarn
```
local(本地单机模式)
Spark standalone(集群模式，简单易用，对资源管理很弱)
Spark on Mesos(与spark同根生)
Spark on Yarn
Spark on K8s
Spark on cloud
```

## 6. Spark 应用场景
- Spark SQL
- Spark Streaming
- MLlib(machine learning)
- 6.4 GraphX(graph) 图计算

## 7.Spark 的应用场景对比


## 8.Spark的优化
- 使用Alluxio
- 优化的前置工作，找瓶颈