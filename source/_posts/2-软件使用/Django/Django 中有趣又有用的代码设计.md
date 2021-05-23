---
title: Django 中有趣又有用的代码设计
date: 2020/08/30
categories:
  - 2-软件使用
  - Django
tags:
  - Django
copyright: true
---


## 通用代码片段

### 初始化类时通过 setattr 和 **kwargs 自动设置实例属性

```python
# django.views.generic.base.py

class View:
	
	def __init__(self, **kwargs):
		for key, value in kwargs.items():
			setattr(self, key, value)
```

### 通过字典进行 Model.objects.get | Model.objects.filter

```python
# rest_framework.generics.py

class GenericAPIView(views.APIView):
	def get_object(self):
		queryset = self.filter_queryset(self.get_queryset())
		
		lookup_url_kwarg = self.lookup_url_kwarg or self.lookup_field
		# 通过 url 传参动态获取对象
		filter_kwargs = {self.lookup_field: self.kwargs[lookup_url_kwarg]}
		obj = get_object_or_404(queryset, **filter_kwargs)
		
		self .check_object_permissions(self.request, obj)
		
		return obj
```