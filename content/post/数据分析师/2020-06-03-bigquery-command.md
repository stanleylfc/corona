+++
title="bigquery 实战（二）| bigquery 命令"
tags=["bigquery"]
categories=["bigquery"]
date="2020-06-03T21:00:00+08:00"
+++
## 2. bigquery 表
### 2.1 显示表结构
```sh
bq show --schema --format=prettyjson projectId:dataset.tablename > tablename.json
```
### 2.2 创建表
```sh
bq mk --table projectId:dataset.tablename /data/tablename.json
```
### 2.3 自动加载数据创建表
```sh
bq load --autodetect --source_format=NEWLINE_DELIMITED_JSON projectId:dataset.tablename  data.json
bq load --autodetect --source_format=CSV --skip_leading_rows=1 projectId:dataset.tablename data.csv
```
## 3. bigquery 查询
