+++
title="Linux运维 实战（五）| promethus"
tags=["Linux运维"]
categories=["Linux运维"]
date="2020-05-31T21:00:00+08:00"
+++
## 1.Prometheus 系统知识要点
![promethus-architecture](/assets/promethus-architecture.png)
### 1.1 Prometheus Server
- Prometheus Server 是 Prometheus 组件中的核心部分，负责实现对监控数据的获取，存储以及查询。 Prometheus Server 可以通过静态配置管理监控目标，也可以配合使用 Service Discovery 的方式动态管理监控目标，并从这些监控目标中获取数据。其次 Prometheus Server 需要对采集到的监控数据进行存储，Prometheus Server 本身就是一个时序数据库，将采集到的监控数据按照时间序列的方式存储在本地磁盘当中。最后 Prometheus Server 对外提供了自定义的 PromQL 语言，实现对数据的查询以及分析。
- Prometheus Server 内置的 Express Browser UI，通过这个 UI 可以直接通过 PromQL 实现数据的查询以及可视化。
- Prometheus Server 的联邦集群能力可以使其从其他的 Prometheus Server 实例中获取数据，因此在大规模监控的情况下，可以通过联邦集群以及功能分区的方式对 Prometheus Server 进行扩展。

### 1.2 Exporters
Exporter 将监控数据采集的端点通过 HTTP 服务的形式暴露给 Prometheus Server，然后 Prometheus Server 通过访问该 Exporter 提供的 Endpoint 端点，即可获取到需要采集的监控数据。

一般来说可以将 Exporter 分为 2 类：
- 直接采集：这一类 Exporter 直接内置了对 Prometheus 监控的支持，比如 cAdvisor，Kubernetes，Etcd，Gokit等，都直接内置了用于向 Prometheus 暴露监控数据的端点。
- 间接采集：间接采集，原有监控目标并不直接支持 Prometheus，因此我们需要通过 Prometheus 提供的 Client Library 编写该监控目标的监控采集程序。例如: Mysql Exporter，JMX Exporter，Consul Exporter等。
### 1.3 AlertManager  
在 Prometheus Server 中支持基于 PromQL 创建告警规则，如果满足 PromQL 定义的规则，则会产生一条告警，而告警的后续处理流程则由 AlertManager 进行管理。在 AlertManager 中我们可以与邮件，Slack 等等内置的通知方式进行集成，也可以通过 Webhook 自定义告警处理方式。AlertManager 即 Prometheus 体系中的告警处理中心。

### 1.4 PushGateway  
由于 Prometheus 数据采集基于 Pull 模型进行设计，因此在网络环境的配置上必须要让 Prometheus Server 能够直接与 Exporter 进行通信。 当这种网络需求无法直接满足时，就可以利用 PushGateway 来进行中转。可以通过PushGateway 将内部网络的监控数据主动 Push 到 Gateway 当中。而 Prometheus Server 则可以采用同样 Pull 的方式从 PushGateway 中获取到监控数据。

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
- 检查配置是否成功
```sh
 $ ./promtool check config prometheus.yml
```
### 4.报警


### 4.1 部署alertmanager
- 检查配置文件：./amtool check-config alertmanager.yml
```
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.163.com:25'
  smtp_from: 'lifeicheng456@163.com'
  smtp_auth_username: 'lifeicheng456@163.com'
  smtp_auth_password: 'lfc3831658'
  smtp_require_tls: false

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1m
  receiver: 'mail'
receivers:
- name: 'mail'
  email_configs:
  - to: '103349002@qq.com'
```

### 4.2 alertmanager 启动和访问
```
# 启动
./alertmanager --config.file=alertmanager.yml
# 访问
http://centos7:9093/
```
### 4.3 配置prometheus 与 alertmanager 通信
- prometheus.yml 配置文件增加下面配置
```yml
alerting:
  alertmanagers:
  - static_configs:
    - targets: ["172.16.232.128:9093"]
```

### 4.3 在premetheus 中创建告警规则
- prometheus.yml 配置文件修改
``` yml
rule_files:
  - "rules/*.yml"
  # - "second_rules.yml"
```
- 创建rules 目录 
```sh
test.yml
```
