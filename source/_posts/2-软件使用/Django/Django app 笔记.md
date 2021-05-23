---
title: Django app 笔记
date: 2020/08/11
categories:
  - 2-软件使用
  - Django
tags:
  - 学习笔记
  - Django
copyright: true
---

## 记

以下内容是我阅读 Django 官方文档 app 相关内容时随手翻译的，内容未经整理

## 源码分析

### django.apps

#### __init__.py
向外部提供 AppConfig apps 两个属性

| 模块        | 提供内容  |
| ----------- | --------- |
| config.py   | AppConfig |
| registry.py | apps      |

#### config.py
提供一个 AppConfig 类

#### registry.py
提供一个 Apps 类


## Application
Django 包含一个已安装 apps 的注册表，它存储配置信息并提供 introspection。它还维护着一个可用模型的列表。

这个注册表简称为 apps，在 django.apps 中可以找到：
```python
from django.apps import apps
apps.get_app_config('admin').verbose_name
```

### 项目和应用（projects and applications）

Project 一词描述了一个 Django Web Application。

一个 projects 的根目录（包含 manage.py 的目录）通常是一个存放所有 projects 中未单独安装的 applications 的容器

Applications 一词描述了一个提供一些功能集的 Python package。Applications 可以在不同 projects 中重复使用。

Applications 是由 models views templates tempalte tags static files URLs middleware等模块自由组合而成。它们一般是用过写入 INSTALLED_APPS 配置相中或一些其他机制（如 URLconfis MIDDLEWARE 设置或模板继承）连接到项目中。

理解 Django applications 是一组与框架各部分交互的代码 很重要。实际上并不存在 Application 对象。然而，在某些情况下 Django 需要和已安装的 Application 进行交互，主要是为了配合，有时也为了 introspection。这也是为什么应用注册表为每个安装的应用在 AppConfig 实例中维护元数据。

Django 没有限制一个 project package 不能同时作为一个 Application，并拥有 models 等。（如果真的把 project package 也当做一个应用，需要将它添加到 INSTALLED_APPS 里）

### 配置应用（Configuring applications）

要配置一个 application，就需要子类化 AppConfig，并在 INSTALLED_APPS 中配置该子类的路径。

当 INSTALLED_APPS 中包含一个应用模块的路径时，Django 会检查该模块中是否存在 default_app_config 变量

如果有，它就是该应用对应的的 AppConfig 子类

如果没有，Django 就使用基础的（默认的）AppConfig 类

default_app_config 允许 Django.contrib.admin 等早于 Django 1.7 的应用选择加入 AppConfig 功能，而不需要用户更新他们的 INSTALLED_APPS。

新的应用程序应该避免使用 default_app_config。相反，他们应该要求在 INSTALLED_APPS 中明确配置相应的 AppConfig 子类的虚线路径。

#### 对于 application 创建者

如果正在创建一个叫做 Rock 的可插拔 app，下面这个例子描述了如何为 admin 提供一个合适的名字 ：

```python
# rock/apps.py

from django.apps import AppConfig

class RockConfig(AppConfig):
    name = "rock"
    verbose_name =  "Rock"
```

可以通过下面的代码将 application 加载到 AppConfig 子类中：

```python
# rock/__init__.py

default_app_config = "rock.apps.RockConfig"
```

这样就可以实现只需要在 INSTALLED_APPS 中设置 "rock" 就可以将 RockConfig 导入。这样就可以在不需要用户更新 INSTALLED_APPS 设置的情况下使用 AppConfig 功能。除了这个使用场景（用例）之外最好避免使用 default_app_config，而在 INSTALLED_APPS 中指定 app config class，如下。

当然，同样可以在 INSTALLED_APPS 中添加 `rock.apps.RockConfig` 实现同样的效果。甚至可以提供多个不同行为的 AppConfig 子类，让用户荣国 INSTALLED_APPS 设置选择其中某一个。

推荐将配置类放在 application 目录下名为 apps 的模块中。但是 Django 并没有强制要求。

配置中必须包含 name 属性来确定这个配置适用于那个应用。

#### 对于 application 用户
略

### Application configraution

```python
# django.app.config
class AppConfig:
    pass
```

#### Configurable attributes

* AppConfig.name
Application 完整的 Python 路径，例如：django.contrib.admin
它在 Django project 中必须是唯一的

* AppConfig.label
Application 的短一点的名字，例如：admin

* AppConfig.verbose_name
Application 人类可读的名字，例如：Administration

* AppConfig.path
指向 Application 文件的完整路径

#### Read-only attributes

* AppConfig.module
Application 的跟模块，例如：<module 'django.contrib.admin' from 'django/contrib/admin/__init__.py'>

* AppConfig.models_module
包含 model 的模块中，例如：<module 'django.contrib.damin.models' from 'django/contrib/admin/models.py>
如果 Application 不包含 models 模块，则可能是 None。需要注意：pre_migrate 和 post_migrate 等与数据库相关的信号支队有 models 模块的应用发出

#### Methods

* AppConfig.get_models()
返回这个 Application 的 Model iterable

* AppConfig.get_model(model_name, require_ready=True)
根据给出的 model_name.model_name 返回 Model（不区分大小写）

如果 Application 中不存在这个模块抛出 LookupError

除非 require_ready 参数为False，否则要求 app 注册表被完全填充。require_ready的行为与app.get_model()中的行为完全相同。

* AppConfig.ready()
子类可以重写这个方法来执行初始化任务，比如注册信号。一旦注册表被完全 populated 它就会被调用

```python
from django.db.models.signals import pre_save

def ready(self):
    # importing model classes
    from .models import MyModel  # or...
    MyModel = self.get_model('MyModel')

    # registering signals with the model's string label
    pre_save.connect(receiver, sender='app_label.MyModel')
```

虽然可以像上面描述的一样访问模型类，但要避免在 ready 中与数据库进行交互

通常情况下 ready 只会被调用一次。但是在某些特殊情况下（特别是在测试中）会被调用不止一次。在这种情况下，要么写idempotent方法，要么在你的AppConfig类上加一个标志，以防止重新运行那些应该被精确执行一次的代码。

#### Namespace packages as apps
略

### Application registry

* apps.ready
布尔值，在注册完全 populated 并且所有 AppConfig.ready() 函数被调用后设为 True

* apps.get_app_configs()
返回一个 AppConfig 实例 iterbale

* apps.get_app_config(app_label)
通过给出的 app_label 返回 Application 的 AppConfig，如果不存在的话抛出 LookupError

* apps.is_installed(app_name)
检查给出的 app 是否存在于注册表。app_name 是完整名字，例如：django.contrib.admin

* apps.get_model(app_label, model_name, require_ready=True)
通过给出的 app_label 和 model_name 返回 Model。

### Initialization process

#### How applications are loaded
当 Django启动，django.setup() 负责填充应用程序注册表

setup(set_prefix=True)

通过以下内容配置 Django：
1. Loading the settings
2. setting up logging
3. 如果set_prefix为True，如果定义了URL解析器脚本前缀，则将其设置为FORCE_SCRIPT_NAME，否则为/。
4. 初始化应用程序注册表

此函数会在一下情况下被自动调用：
1. 当通过 Django WSGI 运行 HTTP 服务时
2. 当运行一个管理命令时
在其他情况下，例如在 Python scripts 中，必须显示调用该函数

应用程序注册表的初始化分为三个阶段。在每个阶段，Django都会按照INSTALLED_APPS的顺序处理所有的应用程序。

1. 首先，Django导入INSTALLED_APPS中的每个项目。

如果是应用配置类，Django会导入应用的根包，由其name属性定义。如果是一个Python包，Django会创建一个默认的应用配置。

在这个阶段，你的代码不应该导入任何模型!

换句话说，你的应用程序的根包和定义应用程序配置类的模块不应该导入任何模型，即使是间接导入。

严格来说，Django允许在加载应用配置后导入模型。但是，为了避免对INSTALLED_APPS的顺序进行不必要的限制，强烈建议不要在这个阶段导入任何模型。

一旦这个阶段完成，对应用配置进行操作的API，如get_app_config()就可以使用了。

2. 然后Django会尝试导入每个应用的models子模块，如果有的话。

你必须在你的应用程序的models.py或models/__init__.py中定义或导入所有模型。否则，应用程序注册表可能在此时没有完全填充，这可能导致ORM故障。

一旦这个阶段完成，对模型进行操作的API，如get_model()就可以使用了。

3. 最后Django会运行每个应用配置的ready()方法。
