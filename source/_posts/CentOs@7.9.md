---
title: CentOs@7.9
date: 2024-05-01
tags: CentOs
categories: 服务器
---
> 关于CentOs@7.9 初次使用注意以及基本内容，持续完善中...



## 首次启动
### 创建文件夹&文件
```shell
cd /
makedir myApp
cd /myApp
echo > myApp.txt
echo '追加' >> myApp.txt
```
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

## 日志查询跟踪
### grep
grep 命令是一个全局查找正则表达式并且打印结果行的命令。
```shell
# 输出查询到的包含 test 的行内容
grep 'test' test.log
# 输出查询到的包含 test 的行内容(不区分大小写)
grep -i 'test' test.log
# 输出查询到的包含 test 的行内容并向下查20行
grep -A 20 'test' test.log
```
### tail
tail 命令可以将文件指定位置到文件结束的内容写到标准输出。
```shell
# 1、输出最后200个字符
tail -c 200 test.log
# 2、从第900个字符开始输出，一直到最后
tail -c +900 test.log
# 3、输出最后20行
tail -n 20 test.log
# 4、从第36行开始输出，一直到最后
tail -n +36 test.log
# 5、输出指定文件的最后十行，同时继续监视文件内容有无变化，新增内容会继续输出，直到按下 [Ctrl-C] 组合键退出 || 文件改名或被删除
tail -f test.log
# 6、输出指定文件的最后十行，同时继续监视文件内容有无变化，新增内容会继续输出，直到按下 [Ctrl-C] 组合键退出
tail -F test.log
# 7、输出指定文件的最后20行，同时继续监视文件内容有无变化，新增内容会继续输出，直到按下 [Ctrl-C] 组合键退出 || 文件改名或被删除
tail -f -n 20 test.log
# 8、指定多个文件并输出文件名
tail -v test1.log test2.log
# 9、指定多个文件不输出文件名
tail -q test1.log test2.log

# 【Ctrl】+【S】 暂停刷新。
# 【Ctrl】+【Q】继续刷新。
# 【Ctrl】+【C】退出 tail 命令。

```



