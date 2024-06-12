---
title: Raspberry pi
date: 2024-05-01
tags:
---
关于Raspberry 初次使用注意以及基本内容，持续完善中...



## 系统烧录
```
    1. 解压下载的系统压缩文件，得到img镜像文件
    2. 将SD使用卡托或者读卡器后，连上电脑
    3. 解压并运行win32diskimager工具（需要先格式化SD卡）
    4. 在软件中选择img（镜像）文件，“Device”下选择SD的盘符，然后选择“Write”
    # 然后就开始安装系统了，根据你的SD速度，安装过程有快有慢。

    ps：树莓派官网提供的烧录工具选择更多点 https://www.raspberrypi.com/software/
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

    # Linux更改文件权限（递归子集）
    chmod -R 777 /home/test
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

## 关于pi安装问题
### apt-get基本命令
```shell
# apt-get可简写apt
    # 更新源文件
    sudo apt-get update
    # 升级所有已安装的包
    sudo apt-get upgrade
    # 清理无用的包
    sudo apt-get autoclean
    # 检查是否有损坏的依赖
    sudo apt-get check
    # 安装 
    sudo apt-get install _name_
    # 移除
    sudo apt-get remove _name_
    # 删除包，包括配置文件
    sudo apt-get remove _name_ --purge
    # 删除包，包括包及其依赖的软件、配置文件
    sudo apt-get autoremove _name_ --purge
```
### 换源
```shell
    # 下面两个源都可使用
    # deb https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
    # deb https://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi

    sudo nano /etc/apt/sources.list # 进入该配置文件更换源

    sudo apt update # 配置完成后执行该命令使生效
```
### 安装mysql
```shell
    # 树莓派无法直接安装mysql，建议使用 mariadb-server-10.0 ，用法一致
    sudo apt-get install mariadb-server-10.0
```
### 解决无密码可登录mysql的问题
```shell
    # 进入mysql后执行
    SET password for 'root'@'localhost'=password('123456'); # 设置密码
    UPDATE user SET plugin='mysql_native_password' WHERE user='root'; # 修改root用户为校验密码
    exit;
    # 退出MySQL执行
    systemctl restart mysql # 重启mysql，执行后选择账户输入密码即可

    cd /etc/mysql # 配置文件下的bind-host改为0.0.0.0，允许任意远程登录

    # mysql内执行
    create user 'example'@'%' identified by 'example123'; # 创建用户
    grant all on example.* to 'example'@'%'; # 指定访问数据库
    flush privileges; # 更新配置信息
```
### 安装node
```shell
    # 下载nvm的包放入/home/pi(也就是~的指向路径)
    sudo nano ~/.bashrc

    # 修改.bashrc文件在后面写入以下内容
        export NVM_DIR="$HOME/.nvm/nvm-0.38.0"
        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
        [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

    # 执行命令更新配置
    source ~/.bashrc

    nvm install 16.19.0
    nvm install 14.21.2
    nvm install 8.17.0
```

## 部署node项目（pm2）
```shell
    # 安装pm2
    npm install pm2 -g
    # 查看版本
    pm2 --version
    # 启动服务
    pm2 start app.js
    # 停止服务
    pm2 stop app.js
    # 查看服务状态
    pm2 monit
    pm2 list
    # 同步进程
    pm2 save
```



