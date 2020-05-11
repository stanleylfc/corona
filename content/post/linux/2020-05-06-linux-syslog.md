+++
title="linux 实战（一）| syslog"
tags=["linux"]
categories=["linux"]
date="2020-04-03T09:00:00+08:00"
toc=true
+++

## 1. 安装
- 一般来说centos都自带了rsyslog,
- 通过源码包来安装
```
源码包下载地址:http://www.rsyslog.com/downloads/download-v8-stable/
```
- 使用yum
```
sudo yum install rsyslog
```

## 2. 开启我们的rsyslog远程服务
### 2.1 编辑配置文件
```
sudo vim /etc/rsyslog.conf

并将以下注释打开
# Provides UDP syslog reception
$ModLoad imudp #打开udp模块
$UDPServerRun 514 #打开udp服务，并监听514端口
# Provides TCP syslog reception
$ModLoad imtcp #打开tcp模块
$InputTCPServerRun 514 #打开tcp服务，并监听514端口

```
### 2.2 重启服务
```
sudo service rsyslog restart
```
### 2.3 查看端口使用
```
netstat -tunlp
```

## 3.使用golang 进行连接写入日志
- tail -f /var/log/syslog
```
sysLog, err := syslog.Dial("tcp", "localhost:514",
    syslog.LOG_WARNING|syslog.LOG_DAEMON, "gameuser")
if err != nil {
    log.Fatal(err)
}
fmt.Fprintf(sysLog, "This is a daemon warning with demotag.")
sysLog.Emerg("And this is a daemon emergency with demotag.")
```

## 4. rsyslog规则
### 4.1 默认规则配置
```
#  Default rules for rsyslog.
#
#			For more information see rsyslog.conf(5) and /etc/rsyslog.conf

#
# First some standard log files.  Log by facility.
#
auth,authpriv.*			/var/log/auth.log
*.*;auth,authpriv.none		-/var/log/syslog
#cron.*				/var/log/cron.log
#daemon.*			-/var/log/daemon.log
kern.*				-/var/log/kern.log
#lpr.*				-/var/log/lpr.log
mail.*				-/var/log/mail.log
#user.*				-/var/log/user.log

#
# Logging for the mail system.  Split it up so that
# it is easy to write scripts to parse these files.
#
#mail.info			-/var/log/mail.info
#mail.warn			-/var/log/mail.warn
mail.err			/var/log/mail.err

#
# Some "catch-all" log files.
#
#*.=debug;\
#	auth,authpriv.none;\
#	news.none;mail.none	-/var/log/debug
#*.=info;*.=notice;*.=warn;\
#	auth,authpriv.none;\
#	cron,daemon.none;\
#	mail,news.none		-/var/log/messages

#
# Emergencies are sent to everybody logged in.
#
*.emerg				:omusrmsg:*

#
# I like to have messages displayed on the console, but only on a virtual
# console I usually leave idle.
#
#daemon,mail.*;\
#	news.=crit;news.=err;news.=notice;\
#	*.=debug;*.=info;\
#	*.=notice;*.=warn	/dev/tty8
```
### 4.2 规则解析之选择器
 - 选择器: Facility(消息源，日志设施)
```
Facility:有0-23种设备
0 kernel messages 
1 user-level messages 
2 mail system 
3 system daemons 
4 security/authorization messages 
5 messages generated internally by syslogd 
6 line printer subsystem 
7 network news subsystem 
8 UUCP subsystem 
9 clock daemon 
10 security/authorization messages 
11 FTP daemon 
12 NTP subsystem 
13 log audit 
14 log alert 
15 clock daemon 
16-23 　　　　local0 - local7   
```
- 选择器: Severity（日志等级）
```
0 Emergency
1 Alert
2 Critical
3 Error
4 Warning
5 Notice
6 Informational
7 Debug
```
```
https://www.mtyun.com/library/how-to-config-rsyslog
```