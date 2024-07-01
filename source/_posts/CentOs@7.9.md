---
title: CentOs@7.9
date: 2024-05-01
tags: CentOs
categories: 服务器
---
> 关于CentOs@7.9 初次使用注意以及基本内容，持续完善中...



## 首次启动
### 新增用户
```shell
    adduser username
    passwd username
```
### 关于yum、rpm基本命令
```shell
    yum install -y name
    yum remove name
    yum clean
    yum localinstall rpm包地址
    # 查询所有rpm包，grep筛选出包含mysql的内容
    rpm -qa | grep mysql
```
### python3.11.1安装
```shell
    # 下载文件到临时目录
    cd /temp
    wget https://www.python.org/ftp/python/3.11.1/Python-3.11.1.tgz
    tar -xzf Python-3.11.1.tgz
    # 安装依赖环境
    yum -y install gcc zlib zlib-devel libffi libffi-devel
    yum install readline-devel
    yum install openssl-devel openssl11 openssl11-devel
    export CFLAGS=$(pkg-config --cflags openssl11)
    export LDFLAGS=$(pkg-config --libs openssl11)
    # 进入下载文件解压地址
    cd Python-3.11.1
    # 指定安装路径
    ./configure --prefix=/usr/bin/python3.11 --with-ssl
    make
    make install
    # 软链指向
    ln -s /usr/bin/python3.11/bin/python3 /usr/bin/python3
    ln -s /usr/bin/python3.11/bin/pip3 /usr/bin/pip3
```
### 修改python默认版本软链指向
```shell
    # 慎重修改， 部分系统安装是默认依赖版本是python2
    sudo rm -rf /usr/bin/python
    sudo ln -s /usr/bin/python3 /usr/bin/python
```
### CentOs@7.9 安装mysql
```shell
    # 检查历史版本
    rpm -qa | grep -i mysql
    rpm -qa | grep -i mariadb
    # 卸载历史版本（如已安装过）
    yum remove -y mysql安装包名称
    yum remove -y mariadb安装包名称
    # 清理残留数据目录及文件-示例
    # 删除安装目录
    whereis mysql
    rm -rf /usr/lib64/mysql /usr/share/mysql
    # 删除数据目录
    rm -rf /var/lib/mysql
    # 删除配置文件
    rm -rf /etc/my.cnf
    # 删除日志文件
    rm -rf /var/log/mysql
    rm -rf /var/log/mysqld.log
    # 删除临时文件
    rm -rf /tmp/mysql*
    # 删除服务和启动脚本
    rm -rf /etc/init.d/mysql
    rm -rf /usr/lib/systemd/system/mysql.service

    # 下载mysql官方yum源
    wget  https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
    # 安装官方yum源
    rpm -ivh mysql80-community-release-el7-3.noarch.rpm
    # 清理yum缓存目录
    yum clean all
    # 重新上传yum缓存
    yum makecache
    # 导入GPG密钥（查看官方最新源https://repo.mysql.com/）
    rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
    # 安装mysql
    yum install -y mysql-community-server mysql-community
    mysql -V
    # 查看临时初始密码
    grep 'temporary password' /var/log/mysqld.log
```



