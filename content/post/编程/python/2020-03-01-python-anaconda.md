+++
title="python 实战（一）| python 安装"
tags=["python"]
categories=["python"]
date="2020-03-01T21:00:00+08:00"
toc=true
+++

## 1. python 安装

## 2. anaconda
020.2.20 更新日志：

本文的初衷是因为安装anaconda的时候你并不知道会包含哪个版本的python，因此我制作了下表
如果你使用的主要的python版本能在下表中找到，那安装对应的anaconda当然更好
但是如果你只是临时想用某个版本的python，或在下表中找不到对应的，你大可以直接安装最新的anaconda，然后用conda create来创建虚拟环境即可，不用非得找到对应的anaconda来装。
最佳的策略是你的机器上只保留一个anaconda，其中包含着你最常用的python版本，然后其他的版本环境全都用虚拟环境来管理。
创建虚拟环境的方法：
例如你要建一个python3.5的虚拟环境（其中myenv是这个环境的名称，可以自定）：

conda create -n myenv python=3.5
然后用以下命令进入该虚拟环境即可：

activate myenv

### 1.安装virtualenv
```
    pip install virtualenv　　#(python2)  
    pip3 install virtualenv　　#(python3)
```

 ### 2.创建venv 虚拟环境
 - 防止不同项目包冲突
 - Python3.3以上的版本通过venv模块原生支持虚拟环境，可以代替之前的virtualenv。(也就是pip install virtualenv)
 ```
    python3 -m venv venv
    venv就是虚拟环境的文件夹，通常取名venv(用户自定义)。  
   --no-site-packages表示不添加系统里面python已安装的第三方库
 ```

 ### 3.启动虚拟环境并安装第三方库
 ```
    venv\Script\activate           #(windows)  
    source venv/bin/activate       #(linux/macos)
    python -m pip install --upgrade pip  # 升级pip
    pip install xxx
 ```

### 4.部署到服务器，环境迁移
#### 4.1开发的电脑上导出 pip list 到 requirements.txt 文件
```
    pip freeze > requirements.txt
```

#### 4.2 另一台新服务器上，安装项目虚拟环境里面的依赖包
```
    pip uninstall -r requirements.txt
    pip install -r requirements.txt
```

### 5.退出虚拟环境
```
    deactivate
```

### 6. python 包升级

```
 pip install --upgrade pip
 pip install facebook-business --upgrade
 pip freeze > requirements.txt 
```

### 7. 卸载 pip 
```
python -m pip uninstall pip
```

### 8 python 版本升级
在ubuntu下安装python 3.7有两种方法:
- 通过使用Deadsnakes PPA中的标准apt工具(本文暂时只介绍这种方法)
- 从源代码进行构建。
前提条件：需要以root用户或具有sudo访问权限的用户身份登录才能在Ubuntu系统上安装软件包。
方法一：使用apt工具安装
使用命令python3 -V，可发现输出为python 3.6.8,　使用以下步骤升级python3 至python 3.7
１)首先更新软件包列表并安装必备组件：
```
sudo apt update
sudo apt install software-properties-common
```
2)接下来，将Deadsnakes PPA添加到您的来源列表中：
```
sudo add-apt-repository ppa:deadsnakes/ppa
```
3)库添加完成后，请使用以下命令安装Python 3.7：
```
sudo apt install python3.7
```
４)至此，Python 3.7已安装在您的Ubuntu系统上，可以使用了。 您可以通过键入以下内容进行验证：
```
python3.7 --version

输出：Python 3.7.5
```
５)将Python 3.6和Python 3.7添加到更新替代项
```
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.7 2
```
6)更新Python 3以指向Python 3.7
```
默认情况下，Python 3.6指向Python3。这意味着当我们运行python3时，它将作为python3.6执行，但我们希望将其作为python3.7执行。
输入以下命令以配置python3：
sudo update-alternatives --config python3
选择python 3.7, 序号2
您应该获得以上输出。 现在键入2并按Enter键以使用Python 3.7。 请记住，选择号可能会有所不同，因此请选择适用于Python 3.7的选择号。
```
７)测试python版本
```
最后输入以下命令测试当前版本的python：
python3 -V
输出应该为 Python 3.7.5
```