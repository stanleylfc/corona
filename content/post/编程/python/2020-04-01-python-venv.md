+++
title="python 实战（二）| virtualenv"
tags=["python"]
categories=["Python"]
date="2020-04-01T21:00:00+08:00"
toc=true
+++

### 1.安装virtualenv
```
    pip install virtualenv　　#(python2)  
    pip3 install virtualenv　　#(python3)
```

 ### 2.创建venv
 ```
    python3 -m venv venv
    venv就是虚拟环境的文件夹，通常取名venv(用户自定义)。  
   --no-site-packages表示不添加系统里面python已安装的第三方库
 ```

 ### 3.启动虚拟环境并安装第三方库
 ```
    venv\Script\activate           #(windows)  
    source venv/bin/activate       #(linux/macos)
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
