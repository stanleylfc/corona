+++
title="Linux运维 实战（五）| promethus"
tags=["Linux运维"]
categories=["Linux运维"]
date="2020-05-31T21:00:00+08:00"
+++

## 2.安装
### 2.1 安装地址
```sh
# 查看环境
$ cat /etc/redhat-release
$ uname -r
# 下载
$ wget https://github.com/prometheus/prometheus/releases/download/v2.7.1/prometheus-2.7.1.linux-amd64.tar.gz

# 创建建prometheus 用户
$ groupadd prometheus
$ useradd -g prometheus -M -s /sbin/nologin prometheus

# 解压并安装
$ tar xf prometheus-2.7.1.linux-amd64.tar.gz -C /opt/
$ cd /opt/
$ ln -s /opt/prometheus-2.7.1.linux-amd64/ /opt/prometheus
$ mkdir -pv /data/prometheus
$ chown -R prometheus:prometheus /opt/prometheus
```

### 2.2 创建 prometheus 系统服务启动文件 
- /opt/prometheus/prometheus.service
- ln -s /opt/prometheus/prometheus.service /usr/lib/systemd/system/prometheus.service
```sh
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=prometheus
Restart=on-failure
ExecStart=/opt/prometheus/prometheus \
  --config.file=/opt/prometheus/prometheus.yml \
  --storage.tsdb.path=/data/prometheus
ExecReload=/bin/kill -HUP $MAINPID
[Install]
WantedBy=multi-user.target
```

### 2.3 修改配置文件
- /opt/prometheus/prometheus.yml
```sh
global:
  scrape_interval: 15s 
  evaluation_interval: 15s 

alerting:
  alertmanagers:
  - static_configs:
    - targets: ["localhost:9093"]

rule_files:
  #- "alert.rules"
  
scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node'
    scrape_interval: 10s
    static_configs:
      - targets: ['localhost:9100']
```

## 3. 安装配置 node_exporter
- 监控服务器 CPU , 内存 , 磁盘 , I/O 等信息，需要在被监控机器上安装 node_exporter 服务。
### 3.1 下载安装
```sh
wget https://github.com/prometheus/node_exporter/releases/download/v0.17.0/node_exporter-0.17.0.linux-amd64.tar.gz

# 解压并安装 node_exporter 服务：
$ tar xf /opt/soft/node_exporter-0.17.0.linux-amd64.tar.gz -C /opt/
$ cd /opt/
$ mv node_exporter-0.17.0.linux-amd64/ node_exporter
$ chown -R prometheus.prometheus /opt/node_exporter
```
### 3.2 创建 node_exporter 系统服务启动文件 
- /usr/lib/systemd/system/node_exporter.service
```sh
# Prometheus Node Exporter Upstart script
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/srv/node_exporter/node_exporter

[Install]
WantedBy=default.target
```

### 3.3 启动 node_exporter 服务：
```sh
$ systemctl daemon-reload
$ systemctl enable node_exporter
$ systemctl start node_exporter
$ systemctl status node_exporter
```
### 3.4 验证服务是否启动成功
- 服务启动后可以用 http://localhost:9100/metrics 测试 node_exporter 是否获取到节点的监控指标。

### 3.5 关联prometheus
- 修改 prometheus 的配置文件/srv/prometheus/prometheus.yml，增加如下内容：
```sh
scrape_configs:
- job_name: 'node'
    scrape_interval: 10s
    static_configs:
      - targets: ['localhost:9100']
```
- 重启 Prometheus 服务：
```
$ systemctl reload prometheus.service
```