---
title:  Win-bat
date: 2024-08-30
tags: Win
categories: Win
---
记录学习一些Windows系统下用到的命令

## 定时任务
### 定时执行脚本
```shell
# 每天17:32执行test脚本
schtasks /create /tn login_task /tr C:\Users\15703\Desktop\login.bat /sc DAILY /st 17:32:00
```
### 定时循环间断执行脚本
```shell
# 当天8:00到21:00每隔2分钟执行test脚本
schtasks /create /tn login_task /tr D:\test.bat /sc minute /mo 2 /st 08:00:00 /et 21:00:00
```
### 开机启动脚本
```shell
schtasks.exe /create /tn "restart" /ru SYSTEM /sc ONSTART /tr "E:\dataojo\commond\restart.bat"
```
### 查看已配置定时任务
```shell
schtasks /query /tn login_task
```
### 结束任务
```shell
schtasks /end /tn login_task
```
### 删除任务
```shell
schtasks /delete /tn login_task /f
```
### 运行任务
```shell
schtasks /run /tn send_msg_task
```

## 关机
### 200秒后关机
```shell
shutdown -s -t 200
```
### 取消关机计划任务
```shell
shutdown -a
```