---
title: DrissionPage
date: 2024-05-01
tags:
---
> DrissionPage 是一个基于 python 的网页自动化工具。它既能控制浏览器，也能收发数据包，还能把两者合而为一。可兼顾浏览器自动化的便利性和 requests 的高效率。它功能强大，内置无数人性化设计和便捷功能。它的语法简洁而优雅，代码量少，对新手友好。


## 1. 运行环境
操作系统：Windows、Linux 或 Mac。
python 版本：3.6 及以上
支持浏览器：Chromium 内核（如 Chrome 和 Edge）

## 2. pip安装及升级
```shell
pip install DrissionPage # pip安装DrissionPage
pip install DrissionPage --upgrade # pip升级DrissionPage
```

## 3. 基本内容
页面类用于控制浏览器，或收发数据包，是最主要的工具。DrissionPage 包含三种主要页面类。根据须要在其中选择使用。
### 3.1 WebPage
    功能最全面的页面类，既可控制浏览器，也可收发数据包(包含了ChromiumPage和SessionPage)
    通过change_mode()方法切换模式(ChromiumPage: 'd', SessionPage: 's')
    ```python
    from DrissionPage import WebPage
    ```
### 3.2 ChromiumPage
    用于只控制浏览器
    ```python
    from DrissionPage import ChromiumPage
    ```
### 3.3 SessionPage
    用于只收发数据包
    ```python
    from DrissionPage import SessionPage
    ```

## 4. 配置工具
很多时候我们须要设置启动参数，可导入以下两个类，但不是必须的。
### 4.1 ChromiumOptions类
    用于设置浏览器启动参数
    ```python
    from DrissionPage import ChromiumOptions
    ```
### 4.2 SessionOptions类
    用于设置Session对象启动参数
    ```python
    from DrissionPage import SessionOptions
    ```
### 4.3 Settings
    用于设置全局配置
    ```python
    from DrissionPage.common import Settings
    ```

## 5. 其他工具
可能需要用到的工具，需要时可以导入。
### 5.1 动作链
    用于模拟一系列键盘和鼠标的操作
    ```python
    from DrissionPage.common import ActionChains
    ```
### 5.2 键盘按键类
    用于键入 ctrl、alt 等按键
    ```python
    from DrissionPage.common import Keys
    ```
### 5.3 By类
    便于项目迁移
    ```python
    from DrissionPage.common import By
    ```
### 5.4 浏览器数据包监听器
    ```python
    from DrissionPage.common import Listener, RequestMan
    ```
### 5.5 easy_set
    保存了一些便捷的 ini 文件设置方法，可选择使用
    ```python
    from DrissionPage.easy_set import *
    ```

## 6. 导入异常
    异常放在以下路径
    ```python
    from DrissionPage.errors import ElementNotFoundError
    ```