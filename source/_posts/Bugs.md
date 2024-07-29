---
title: Bugs
date: 2024-05-01
tags: Bugs
categories: 前端
---
> 前端项目开发过程中碰到的有意思的问题记录，持续更新...

## 项目安装依赖
### 问题一
1. 报错内容
```shell
git .EXE ls-remote -h -t ssh://git@github.com/************.git
```
2. 解决方案
```shell
# 全局配置 将使用到的依赖仓库的地址git更改为https
git config --global url."https://".insteadOf git://
npm cache clean --force
npm config set registry https://registry.npmmirror.com/
npm install
```