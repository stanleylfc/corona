+++
title="python 实战（三）| python 用docker安装"
tags=["python"]
categories=["Python"]
date="2020-04-02T00:00:00+08:00"
toc=true
+++

### 基础环境
    - Linux
    - Python 3.8
    - Docker

### 1. 容器
[docker](https://www.docker.com/products/docker-desktop)
```
$ docker pull ubuntu

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              4e5021d210f6        13 days ago         64.2MB
```

创建容器
```
$ docker create -ti --privileged --name py3 -p 10000:80 -p 10001:8888 -v /mac/py3:/root/py3 -w /root ubuntu bash -l
```

### 启动
```
$ docker start -ai py3
```
Docker 后台运行的快捷键是：Ctrl P + Ctrl Q

### 安装
更新源，安装 Python 3.8
```
$ apt update

$ apt install curl
$ apt install python3.8 python3.8-distutils
```
安装 pip 包管理器
```
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python3.8 get-pip.py

$ pip3 -V
pip 20.0.2 from /usr/local/lib/python3.8/dist-packages/pip (python 3.8)
```
安装附加工具
```
$ pip3 install ipython ipdb jupyter
```

### 环境
```
$ jupyter notebook password
Enter password:
Verify password:
[NotebookPasswordApp] Wrote hashed password to /root/.jupyter/jupyter_notebook_config.json

$ jupyter notebook --ip=0.0.0.0 --allow-root
[I 11:30:25.035 NotebookApp] Serving notebooks from local directory: /root
[I 11:30:25.035 NotebookApp] The Jupyter Notebook is running at:
[I 11:30:25.035 NotebookApp] http://cce1fcf67196:8888/
```

现在，可以在宿主（macOS）用浏览器打开。
```
http://localhost:10001
```
备注：以上内容参考雨痕老师