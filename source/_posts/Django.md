---
title: Django
date: 2023-11-10 08:00:00
tags:
---
关于Python的服务端框架-Django(有大量的基础服务内容，可创建多模块分配)。当前使用环境：python --version 3.11.1、pip --version 23.3.1。

## 安装Django 及 其他依赖包
```shell
pip install django # 框架依赖包 version 4.2.7
pip install django-cors-headers # 处理跨域的依赖包
pip install pymysql # mysql模块依赖包
```
## 依赖记录
```shell
# 创建依赖记录
pip freeze > requirements.txt
# 依赖记录安装
pip install -r requirements.txt
```

## 创建项目
```shell
Django-admin startproject templateName
```

## 基础配置
### 处理跨域访问
```python
# 基础配置文件配置以下内容
ALLOWED_HOSTS = ['*']

CORS_ALLOW_CREDENTIALS = True
CORS_ORIGIN_ALLOW_ALL = True
CORS_ORIGIN_WHITELIST = ('http://localhost', 'http://127.0.0.1', 'http://0.0.0.0')
SECURE_CROSS_ORIGIN_OPENER_POLICY = None # 默认值为same-origin

CORS_ALLOW_METHODS = (
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
    'VIEW',
)

CORS_ALLOW_HEADERS = ('*',)
```
### 模块创建
```shell
python manage.py startapp AppOne # 创建AppOne模块
python manage.py startapp AppTwo # 创建AppTwo模块
```

### 模块注册
```python
# 模块创建完成后需要注册
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'AppOne.apps.ApponeConfig', #注册AppOne
    'AppTwo.apps.ApptwoConfig', #注册AppTwo
]
```

### 数据库配置
```python
# __init__.py
import pymysql  # 导入第三方模块，用来操作mysql数据库
pymysql.install_as_MySQLdb()
# settings.py
DATABASES = {
    'default': {
        # django默认数据库
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
        # mysql远程数据库
        # 'ENGINE': 'django.db.backends.mysql', # 更改为mysql
        # 'NAME': 'example', # testsql数据库名
        # 'USER': 'example', # 数据库的用户名
        # 'PASSWORD': 'example123', # 密码
        # 'HOST': '*.*.*.*',
        # 'PORT': '3306', # 端口号，默认为3306
    }
}
```
### Django模型
```python
# 定义AppOne的模型  />AppOne>models.py
from django.db import models

# Create your models here.
class Book(models.Model):
    id = models.AutoField(primary_key=True) # id 会自动创建,可以手动写入
    title = models.CharField(max_length=32) # 书籍名称
    price = models.DecimalField(max_digits=5, decimal_places=2) # 书籍价格
    publish = models.CharField(max_length=32) # 出版社名称
    pub_date = models.DateField() # 出版时间

```
```shell
python manage.py makemigrations AppOne # 映射AppOne的models
python manage.py migrate 同步AppOne的models到数据库
```

### 后台管理员配置
```shell
# 需要先映射同步数据库
python manage.py makemigrations 映射models
python manage.py migrate 同步到数据库

# 创建超级用户
python manage.py createsuperuser
# http://ip:端口/admin/ 访问后台
```

### 启动服务
```shell
python manage.py runserver 0.0.0.0:5000 # 启动到局域网内5000端口
python manage.py runserver 127.0.0.1:5000 # 启动到本地5000端口
```

## 路由配置
```python
# urls.py
from django.urls import path, include # include用于多项目时分发

from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls),
    path("user/", include("user.urls")),
]
```

## 模块视图
### 子路由配置
```python
# />AppOne>urls.py
from django.urls import path
from django.urls import re_path # 用re_path 需要引入

from AppOne import views

urlpatterns = [
    path('search/', views.searchUser), # 普通路径
    re_path(r'^index1/([0-9]{4})/$', views.index1), # 正则路径（无名分组）http://127.0.0.1:5000/user/index1/2023/
    re_path("^index2/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$", views.index2), # 正则路径（路由形参）http://127.0.0.1:5000/user/index2/2023/05/
    # r:代表的是原生字符串（raw）
    # ^:代表以什么开头
    # $:代表的是以什么结尾
    # /article/list/(year)/
    # 在正则表达式中定义一个变量，参数，就需要用（）进行捕获参数。
    # 给捕获的参数去一个名字，就可以使用<>,（?P<year>）
    # \d:代表是0-9之间的数字
    # {4}：代表的是这样的数字有4个。
    # 在我们的$符号前面有一个/，代表的是要以/结尾。
]
```
### 子模型注册Admin
```python
# />AppOne>admin.py
from django.contrib import admin
from user.models import Book
 
# Register your models here.
admin.site.register([Book])
```
### 视图内容
```python
# />AppOne>views.py
from django.shortcuts import render

# Create your views here.
from django.http import HttpResponse, JsonResponse
from django.core import serializers # 格式化json serializers.serialize("json", models.Book.objects.all())
from user import models
import json
def searchUser(request):
    # 请求接口返回示例
    if request.method == "GET":
        # GET
        name = request.GET.get('name')
        return HttpResponse("user：" + name)
    elif request.method == "POST":
        # POST
        name = request.POST.get('name')
        return HttpResponse("user：" + name)  # 字符串作为返回内容
    
def index1(request, year):
    # 请求接口返回示例
    return HttpResponse(year) # 一个形参代表路径中一个分组的内容，按关键字对应匹配

def index2(request, year, month):
    # 请求接口返回示例
    return HttpResponse(year + '-' + month) # 一个形参代表路径中一个分组的内容，按关键字对应匹配
```
