---
title: Django 不同环境下使用不同设置文件
date: 2019/7/24
categories:
  - 编程框架
  - Django
tags:
  - Django 笔记
copyright: true
---

## 起因

在使用 `Django` 开发项目的过程中总是会遇到生产环境和开发环境需要使用不同设置文件的情况，之前我都是在开发环境做完开发在后上传到服务器然后在服务器上修改设置文件，一来二去的就觉得很麻烦，恰巧昨天看到了这样一篇文章 [Django 工程结构以及利用 Git 分支进行生产环境切换][1] 里面描述了一种用利用 `Git` 分支在不同环境下切换设置文件的方法，但是说实话我没明白他这样做的意义，难道要为不同的环境创建不同的 `Git` 分支吗？可能是我 `Git` 学的不深不是很能理解，但是这篇文章还是给我提供了很好的思路。然后我又通过 `Google` 看到了另一篇文章 [Django 开发环境与生产环境的区分][2]，结合这两篇文章我自定义了下面的方案。

## 方案

拆分设置文件，在不同环境下设置不同的环境变量然后通过 `Python` 的 `os.environ.get` 方法获取当前环境的环境变量，根据不同的环境变量 `import` 不同的设置文件，具体如下：

### 拆分设置文件

在 `Django` 的 `project` 目录下新建 `settings` 目录，然后根据自己的需求在 `settings` 目录下创建诸如 `base.py, dev.py, pro.py, test.py` 之类的设置文件，我的需求比较简单只分了 `base.py, dev.py, pro.py` 三个文件，它们分别表示：基础设置，开发环境设置和生产环境设置。

![拆分设置文件][3]

### 给拆分后的设置文件设置入口

一般情况下都是以 `base.py` 为拆分后的设置文件入口

```python
...

# 根据环境变量导入不同设置文件
# 如果环境变量中存在 ENV（具体值可行自定义）则判定为生产环境导入生产环境设置
# 否则则判定为开发环境导入开发环境设置
if os.environ.get('ENV', None):
    from .pro import *
else:
    from .dev import *

...
```

### 修改相关文件

拆分完设置文件后需要修改那些引用了设置文件的文件：`wsgi.py, manage.py`

```python
# wsgi.py
# 修改此文件以确保通过 uwsgi 启动时程序能够正确的找到设置文件
import os
from django.core.wsgi import get_wsgi_application

# 这一行是重点，意思是添加环境变量 DJANGO_SETTINGS_MODULE 为 backend.settings.bases
# 具体值应根据个人项目的设置文件入口路径自行设置
os.environ['DJANGO_SETTINGS_MODULE'] = 'backend.settings.base'

application = get_wsgi_application()
```

```python
# manage.py
# 修改此文件以确保通过 python manage.py runserver 启动时程序能够正确的找到设置文件

#!/usr/bin/env python
import os
import sys

if __name__ == '__main__':
    # 这一行是重点，意思是添加环境变量 DJANGO_SETTINGS_MODULE 为 backend.settings.bases
    # 具体值应根据个人项目的设置文件入口路径自行设置
    os.environ['DJANGO_SETTINGS_MODULE'] = 'backend.settings.base'
    try:
        from django.core.management import execute_from_command_line
    except ImportError as exc:
        raise ImportError(
            "Couldn't import Django. Are you sure it's installed and "
            "available on your PYTHONPATH environment variable? Did you "
            "forget to activate a virtual environment?"
        ) from exc
    execute_from_command_line(sys.argv)
```

### 生产环境配置

最后只需要在生产环境设置与入口文件匹配的环境变量即可：

```powershell
# vim /etc/profile
# 在最后一行添加：
export ENV="SERVER"
# 随便设置什么值都行但是必须与入口文件匹配
```

### 开始使用

做完以上配置以后就可以开始使用了，开发生产两套配置，不需要再上传后另外修改文件了。

## 总结

通过方案能够看出整个过程的两个核心点是：
1. 拆分设置文件
2. 找到一个能通过 `Python` 区分不同环境的点

只要能完成以上两点就能自定义属于自己的方案。

[1]: https://smartkeyerror.com/%E5%B7%A5%E7%A8%8B%E7%BB%93%E6%9E%84.html
[2]: http://code4fs.xyz/article/2/
[3]: https://img.blanc.site//wiki/img/20190725100859.png