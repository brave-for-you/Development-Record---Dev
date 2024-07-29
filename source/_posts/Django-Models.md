---
title: Django-Models
date: 2024-05-01
tags: Django
categories: Python
---
> 关于Django的模型类的数据增删改查

```python
from user import models
```

## 增
### save()
```python
book = models.Book(title="教程", price=300, publish="出版社", pub_date="2008-8-8") 
book.save()
```
### create()
```python
books = models.Book.objects.create(title="如来神掌", price=200, publish="功夫出版社", pub_date="2010-10-10")
```
## 删
### 符合条件的第一个
```python
books=models.Book.objects.filter(pk=8).first().delete()
```
### 符合条件的
```python
books=models.Book.objects.filter(pk__in=[1, 2]).first().delete()
```
### 符合条件的最后一个
```python
books=models.Book.objects.filter(pk=8).last().delete()
```
## 改
### save()
```python
books = models.Book.objects.filter(pk=7).first()
books.price = 400
books.save()
```
### update()
```python
books = models.Book.objects.filter(pk__in=[7, 8]).update(price=888)
```
## 查
> 查询建议：使用values()配合filter()、exclude()等方法处理表查询，values()返回的是字典的集合，处理方便
### 所有
```python
# all(): 类似于 list，里面放的是一个个模型类的对象，可用索引下标取出模型类的对象 比如def就是一个模型类
books = models.Book.objects.all()
```
### 第一条
```python
books = models.Book.objects.first()
```
### 最后一条
```python
books = models.Book.objects.last()
```
### 部分字段
```python
# 返回内容键对值 values(): 类似于 list，里面不是模型类的对象，而是一个可迭代的字典序列 比如{name：123}就是字典
books = models.Book.objects.values("pk", "price")
# 返回内容只有值
books = models.Book.objects.values_list("price", "publish")
```
### 单字段并去重
```python
books = models.Book.objects.values("price").distinct()
```
### 符合条件
```python
# filter(): 类似于 list，里面放的是一个个模型类的对象，可用索引下标取出模型类的对象 比如def就是一个模型类
books=models.Book.objects.filter(pk=8)

# 查（符合条件-日期单查年）
books=models.Book.objects.filter(pub_date__year=2023) # pub_date: 2023-10-15
# 查（符合条件-日期单查月）
books=models.Book.objects.filter(pub_date__month=10) # pub_date: 2023-10-15
# 查（符合条件-日期单查日）
books=models.Book.objects.filter(pub_date__day=1) # pub_date: 2023-10-1
# 查（__contains 包含，= 号后面为字符串 区分大小写）
books=models.Book.objects.filter(title__contains="菜")
# 查（__icontains 包含 不区分大小写）
books=models.Book.objects.filter(title__icontains="python")
# 查（title__startswith 指定内容开头）
books=models.Book.objects.filter(title__startswith="菜")
# 查（title__endswith 指定内容结尾）
books=models.Book.objects.filter(title__endswith="教程")
# 查（条件in查询）
books = models.Book.objects.filter(price__in=[200,300])
# 查（区间range查询）
books = models.Book.objects.filter(price__range=[200,300])
# 查（大于）
books = models.Book.objects.filter(price__gt=200)
# 查（大于等于）
books = models.Book.objects.filter(price__gte=200)
# 查（小于）
books=models.Book.objects.filter(price__lt=300)
# 查（小于等于）
books=models.Book.objects.filter(price__lte=300)
```
### 不符合条件
```python
books = models.Book.objects.exclude(publish='出版社', price=300)
```
### 排序
```python
# 查询所有，按照价格升序排列 
books = models.Book.objects.order_by("price")
# 查询所有，按照价格降序排列
books = models.Book.objects.order_by("-price")
```
### 数量
```python
# 查（符合条件数量）
books = models.Book.objects.filter(price=200).count() 
```