---
title: Raspberry pi
date: 2023-10-16 15:00:00
tags:
---
关于Raspberry 初次使用注意以及基本内容，待完善...



## 系统烧录
```
    1. 解压下载的系统压缩文件，得到img镜像文件
    2. 将SD使用卡托或者读卡器后，连上电脑
    3. 解压并运行win32diskimager工具（需要先格式化SD卡）
    4. 在软件中选择img（镜像）文件，“Device”下选择SD的盘符，然后选择“Write”
    # 然后就开始安装系统了，根据你的SD速度，安装过程有快有慢。
```

## 首次开机
### 关于账号
```shell
    # 用户名：pi
    # 密码：yahboom / raspberry

    # root用户原始系统是没有开启的，自己设置密码就可以
    sudo passwd root # 输入两次密码确认
    sudo passwd --unlock root # 解锁root用户

    # 切换root：
    su # 确认后输入密码
```
### 进入后界面操作
```shell
    # 打开树莓派系统下的命令行终端（Ctrl+Alt+T）
    
    ifconfig # 查看我们的ip地址

    sudo raspi-config # 进入到树莓派系统配置界面
```
### 关于几个常用命令
```shell
    # 查看操作系统版本
    cat /proc/version

    # 查看主板版本
    cat /proc/cpuinfo

    # 查看SD存储卡剩余空间
    df -h

    # 查看ip地址
    ifconfig

    # 压缩
    tar –zcvf  filename.tar.gz dirname
    # 解压
    tar –zxvf filename.tar.gz
```

## 关机注意
```shell
    # 不能直接拔掉电源，会造成树莓派数据无法及时保存而丢失
    # 可以按需选择相关的终端命令操作
    sudo poweroff # 关闭电源
    sudo shutdown -h now # 立刻关机
    sudo shutdown -r now # 立刻重启
    sudo reboot # 重启
    sudo shutdown -h +2 # 2分钟之后关机
```


