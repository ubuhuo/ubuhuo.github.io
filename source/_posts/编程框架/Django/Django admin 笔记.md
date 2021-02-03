---
title: Django admin 笔记
date: 2020/08/11
categories:
  - 编程框架
  - Django
tags:
  - Django 笔记
copyright: true
---

## 记

一下内容是我阅读 Django 官方文档 admin 相关内容时随手翻译的，内容未经整理

## Overview

admin 的 requirements：
1. 添加 `dango.contrib.admin` 和它依赖的 `django.contrib.auth` `django.contrib.contenttypes` `django.contrib.messages` `django.contrib.sessions` 到 `INSTALLED_APPS`
2. 通过在项目设置项 `TEMPLATES` 的选项 `OPTIONS` 的选项 `context_processors` 下添加  `django.contrib.auth.context_processors.auth` 和 `django.contrib.messages.contxet_processors.messages` 来配置  `DjangoTemplates`
3. 如果是自定义的 MIDDLEWARE 设置项， `django.contrib.auth.middleware.AuthenticationMiddleware` 和 `django.contrib.messages.middleware.MessageMiddleware` 必须被包括在其中
4. 配置 admin URL 到项目 URLconf

在进行这些配置后可以通过访问绑定的 url 访问 admin（默认是 `/admin/`）

可以使用默认的 `createsuperuser` 命令来创建一个用来登录的用户。一般来说用户需要将 `is_superuser` 和 `is_staff` 属性都设置为 True 才能登录 admin。

最后，确定应用程序的哪些模型可以在展示在 admin 中。按照 `ModelAdmin` 中的描述，在管理员处注册它们。

## ModelAdmin
`ModelAdmin` 类是 model 在 admin 接口中的表达方式。通常这些内容会存储在应用下的 `admin.py` 文件下。

### Discovery（发现） of admin files
当把 `django.contrib.admin` 加入到 `INSTALLED_APPS`，Django 会在 app 目录中自动查找 admin 模块并引用它

* class apps.AdminConfig
这是 admin 的默认的 AppConfig 类。它会在 Django 启动时调用 autodiscover() 方法

* class apps.SimpleAdminConfig
和上面的类似，但是不调用 autodiscover

* default_site
一个点状的导入路径，用于导入默认的管理站点的类或返回站点实例的可调用类

默认为'django.contrib.admin.sites.AdminSite'

* autodiscove()

该功能试图**在每个安装的应用程序中导入一个 admin 模块**，这样的模块被期望向 admin 注册模型。

通常情况下，你不需要直接调用这个函数，因为当Django启动时，AdminConfig会调用它。

如果你使用的是自定义的 AdminSite，通常会将所有的 ModelAdmin 子类导入到你的代码中，并将它们注册到自定义的AdminSite中。

在这种情况下，为了禁用自动发现，

你应该在INSTALLED_APPS设置中加入'django.contrib.admin.apps.SimpleAdminConfig'而不是'django.contrib.admin'。

### ModelAdmin options
ModelAdmin 非常灵活。它有几个选项来处理自定义界面。所有选项都定义在 ModelAdmin 子类上。

* ModelAdmin.action | 展示页
在 change list page 提供的操作列表

* ModelAdmin.actions_on_top | 展示页
* ModelAdmin.actions_on_botton | 展示页
控制动作栏在哪个位置出现，默认 top

* ModelAdmin.actions_selection_counter | 展示页
是否在 action dropdwon 下显示计数器（默认显示）

* ModelAdmin.date_hierarchy | 展示页
将此选项设置为 Modle 中某一个 DateField 或 DateTimeFile 字段，在 List 页面的顶部会显示一个时间选择条

* ModelAdmin.emty_value_display | 展示页
当某个值为空的时候填充什么

* ModelAdmin.exclued | 详情页（新增，更改）
给定一个字段名称的列表或元组，将其中的字段从详情页中排除

* ModelAdmin.fields | 详情页（新增，更改）
fields 能够接受与 list_display 同类型的值，只是不能接受可调用项

model 和 model admin methods 只能在 readonly_fields 字段上可用

如果要将多项内容放在一行，需要将所需选项放入一个元组中（不知道列表行不行）

如果 fields 和 fieldsets 选项都不存在，Django 将默认在单个 fieldset 中显示每一个不是 AutoField 且 editable = True 的字段，顺序与在 model 中顺序相同

* ModelAdmin.fieldsets | 详情页（新增，更改）
field set =》 字段 集合，管理字段集合的一个选项

一个二元元组的元组（列表应该也行吧），其中每个二元元祖代表页面上的一组字段

二元元祖格式：(name, field_options)
name 表示字段集标题的字符串
field_options 关于字符集信息的字典，包括要在其中显示的字段列表

field_options 字段可配置的键：
    * fields：字段名称的元组，与 fields 参数相同，必须
        * classes 一个包含额外应用到此 fieldset 上的 CSS 类的元组或列表，有两个默认的选项 coolapse 和 wide
        * description：描述内容，现实在字段集合顶部标题下，可以直接写HTML

* ModelAdmin.filter_horizontal | 新增或更改页面
为 ManyToManyFields 添加选择框

```python
# person admin.py

@register(Person)
class PersonAdmin(admin.ModelAdmin):
	filter_horizontal = ("task",)

```

TODO 插入图片

* ModelAdmin.filter_vertical | 展示页

和 filter_horizontal 类似，只是 从横向变成纵向

* ModelAdmin.form | 新增或修改页面
默认情况下会为 Model 动态创建一个 ModelForm。用于创建在新增或修改页面展示。

可以通过这个属性提供一个自己的表单来覆盖表单行为，或者可以使用自定义默认表单而不是自动生成（请看 Adding custom validation to the admin）

如果 ModelForm 和 ModelAdmin 都定义了 excude ModelAdmin 优先

* ModelAdmin.formfield_overrides | 每太看懂不过感觉用不上
This provides a quick-and-dirty way to override some of the Field options for use in the admin

* ModelAdmin.inlines
略

* ModelAdmin.list_display | 展示页
一个字段名元组（列表也行），选择需要在 List 页面展示哪些字段

可以自定义一些 callable 对象然后赋值给它用来展示

```python
def upper_case_name(obj):
    return ("%s %s" % (obj.first_name, obj.last_name)).upper()

# 通过这个属性来设置展示名称
upper_case_name.short_description = 'Name'

class PersonAdmin(admin.ModelAdmin):
    list_display = (upper_case_name,)

#-----------


class PersonAdmin(admin.ModelAdmin):
    list_display = ('upper_case_name',)

    def upper_case_name(self, obj):
        return ("%s %s" % (obj.first_name, obj.last_name)).upper()
    upper_case_name.short_description = 'Name'

#----------------

from django.contrib import admin
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=50)
    birthday = models.DateField()

    def decade_born_in(self):
        return self.birthday.strftime('%Y')[:3] + "0's"
    decade_born_in.short_description = 'Birth decade'

class PersonAdmin(admin.ModelAdmin):
    list_display = ('name', 'decade_born_in')
```

关于 list_dispaly 的一些特殊情况：
1. 如果字段是 ForeignKey，Django 会显示该字段的 __str__
2. ManyToManyField 不能被写在这里面，因为这需要对表中的每一个执行一个单独的 SQL 语句。如果你确实想这么做，可以提供一个 model 自定义方法，并且将这个方法名写在 list_display 中
3. 如果是布尔值，Django将显示一个 on 或 off 的图标
4. 如果是给定 model ModelAdmin 或callable，Django会对输出进行 HTML转义

```python
from django.contrib import admin
from django.db import models
from django.utils.html import format_html

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    color_code = models.CharField(max_length=6)

    def colored_name(self):
        return format_html(
            '<span style="color: #{};">{} {}</span>',
            self.color_code,
            self.first_name,
            self.last_name,
        )

class PersonAdmin(admin.ModelAdmin):
    list_display = ('first_name', 'last_name', 'colored_name')
```

通常，list_display 中不是实际数据库字段的元素不能用于排序，但如果 list_display 的元素表示某个数据库字段，则可以通过设置项的 admin_order_field 属性来表明。

```python
from django.contrib import admin
from django.db import models
from django.utils.html import format_html

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    color_code = models.CharField(max_length=6)

    def colored_first_name(self):
        return format_html(
            '<span style="color: #{};">{}</span>',
            self.color_code,
            self.first_name,
        )

    colored_first_name.admin_order_field = 'first_name'

class PersonAdmin(admin.ModelAdmin):
    list_display = ('first_name', 'colored_first_name')
```

list_display 中的字段名将作为 css class 出现在 HTML 中

Django 将按照以下顺序 interpret 每一个 list_display 上的元素：
1. model 的字段
2. callable
3. ModelAdmin 属性字符串
4. model 属性字符串

* ModelAdmin.list_display_links | 展示页
规定 list_display 中哪个字段链接到对应对象的更改页面，默认是列表的第一列

可以设置多个，但必须在 list_display 中，也可以置空

* ModelAdmin.list_editable | 展示页

元组（列表），必须包含在 list_display 中不能喝 list_display_links 重合

允许在展示页直接对某些元素进行修改

* ModelAdmin.list_filter | 展示页
元组（列表），必须包含在 list_display 中

在展示页右侧新增一个通过规定内容进行过滤的过滤器

允许是以下类型之一：
1. 字段名，可以通过双下划线进行跨关系查找
2. 继承自 django.contrib.admin.SimplateFilter 的类，需要提供 title 和 parameter_name 属性重写 lookups 和 queryset 方法
3. 元组，第一个元素是字段名，第二个是 django.contrib.admin.SimplateFilter 类的子类

* ModelAdmin.list_max_show_all | 展示页

管理 show all admin change list page 可以显示多少项，默认200
只有当总数少于设置项时才会显示 show all 按钮

* ModelAdmin.list_per_page | 展示页

展示页每页显示数目，默认100

* ModelAdmin.list_select_related | 展示页？

> select_related主要针一对一和多对一关系进行优化。
> select_related使用SQL的JOIN语句进行优化，通过减少SQL查询的次数来进行优化、提高性能。

设置 list_select_related 告诉 Django 使用 select_related() 在更改页面检索对象列表时

布尔值，列表或元组，默认为 False
当时 True 时，始终调用 select_related；False时，查看 list_display 在存在 ForeignKey 时调用 select_related

使用元组（或列表）进行更细粒度的控制，空元组会阻止 Django 使用 select_related，任何其他的元组都会直接传递给 select_related 作为参数

如果需要根据请求指定一个动态值，可以实现一个get_list_select_related（）方法。

* ModelAdmin.ordering | 展示页

设置排序，列表或元组
如果需要指定动态顺序（例如根据用户或语言），可以实现get_ordering（）方法。

* ModelAdmin.paginator | 展示页

规定要用于分页的 paginator 类 默认 `django.core.paginator.Paginator`

自定义分页类需要继承 `django.core.paginator.Paginator` 而却需要提供 `ModelAdmin.get_paginator()`

* ModelAdmin.prepopulated_fielads

将预填充字段设置为字典，将字段名称映射到应预填充的字段

* ModelAdmin.preserve_filters | 展示页面

站点会在创建、编辑或删除对象后在列表视图中保留过滤器。

* ModelAdmin.radio_fields | 创建或修改

默认情况下，Django的管理员对 ForeignKey 或设置了选项的字段使用选择框界面（<select>）。如果一个字段出现在 radio_fields 中，Django 将使用一个单选按钮接口。

字典形式，有HORIZONTAL 和 VERTICAL 两个选项

```python
radio_fields = {"field_name": admin.VERTICAL}
```

* ModelAdmin.autocomplete_fields | 新增，修改

元组或列表，里面放入一对多或多对多字段，在新增或修改的时候可以通过异步搜索查找需要的选项

* ModelAdmin.raw_id_fields | 新增，修改

元组或列表，里面放入一对多或多对多字段，默认情况下一对多或多对多会使用下拉菜单，但如果将他们放入这个选项中将取消他们的下拉菜单直接给出一个输入框供用户手动输入，并且给出一个查找图标，点击图标后可以通过一个弹出页面查看全部选项并进行选择

* ModelAdmin.readonly_fields | 新增，修改

元组或列表，放置在此选项的字段会从新增或修改页面消失

* ModelAdmin.save_as | 修改页面

默认为 False

默认情况下修改页面会显示：保存并增加另一个、保存并继续编辑、保存三个按钮，如果为True会将保存并新增另一个修改为保存为新对象

* ModelAdmin.save_as_continue | 修改页面

默认为 True

如果为 False 在修改对象之后重定向到 changelist view

* ModelAdmin.save_on_top | 新增 | 修改

一般来说保存按钮在底部，如果设为 True 底部和顶部都有

默认为 False

* ModelAdmin.search_fields | 展示页

元组或列表

设置此选择项后会在展示页显示一个搜索栏

被填入的字段应该是文本字段，也可以使用双下划线来查找 ForeignKey 或 ManyToManyField 的内容

using the icontains lookup

example, if search_fields is set to ['first_name', 'last_name'] and a user searches for john lennon, Django will do the equivalent of this SQL WHERE clause:

```mysql
WHERE (first_name ILIKE '%john%' OR last_name ILIKE '%john%')
AND (first_name ILIKE '%lennon%' OR last_name ILIKE '%lennon%')
```

如果不想使用icontains作为查找，可以通过将其附加到字段来使用任何查找。例如，您可以通过将search_fields设置为 `['first_name_uuexact']` 来使用 `exact`。

* ModelAdmin.show_full_result_count | 展示页

是否在展示页显示全部的结果数，默认为 True，默认情况下会产生一个额外的查询，而且如果数据量特别大的话这个查询会很消耗资源

* ModelAdmin.sortable_by | 展示页

如果要对某些列禁用排序，请将sortable_by设置为要排序的list_display子集的集合（例如list、tuple或set）。空集合将禁用所有列的排序。

如果需要动态指定此列表，请改为实现get_sortable_by（）方法。

* ModelAdmin.view_on_site | 新增，修改

此属性用来控制是否显示 View on stie 连接，这个链接会把你带到一个可以显示保存对象的URL

#### custom template options

使用以下选项覆盖ModelAdmin视图使用的默认模板：

* ModelAdmin.add_form_template

Path to a custom template, used by add_view().

* ModelAdmin.change_form_template

Path to a custom template, used by change_view().

* ModelAdmin.change_list_teimplate

Path to a custom template, used by changelist_view().

* ModelAdmin.delete_confirmation_template

Path to a custom template, used by delete_view() for displaying a confirmation page when deleting one or more objects.

* ModelAdmin.delete_selected_confirmaton_template

Path to a custom template, used by the delete_selected action method for displaying a confirmation page when deleting one or more objects. See the actions documentation.

* ModelAdmin.object_history_template

Path to a custom template, used by history_view().

* ModelAdmin.popup_response_template

Path to a custom template, used by response_add(), response_change(), and response_delete().

### ModelAdmin methods

* ModelAdmin.save_model(request, obj, form, change)

此方法需要传入一个 HttpRequest 一个 model 实例 一个 ModelForm 实例 一个决定此操作是新增还是修改的布尔值

重写这个方法允许做一些前置或者后置保存操作，调用 super().save_model() 使用 Model.save() 来保存对象

* ModelAdmin.delete_model(request, obj)

此方法需要传入一个 HttpRequest 对象和一个 model 实例

重写此方法允许做一些前置或后置操作

调用 super().delete_model() 使用 Model.delete() 来删除对象

* ModelAdmin.delete_queryset(request, queryset)

重写此方法以自定义 删除选定对象 操作的删除过程

* ModelAdmin.save_formset(request, form, formset, change)

此方法需要传入一个 HttpRequest 一个 父类 ModelForm 实例 和一个决定是执行新增操作还是执行修改操作的布尔值

* ModelAdmin.get_ordering(request)

返回一个类似 ordering 参数的列表或元组

* ModelAdmin.get_search_results(request, queryset, search_term)

此方法提供使用 search 组件的搜索结果。接受一个 request和一个queryset使用当前filter和用户提供的搜索内容。还接受一个布尔值用来规定结果中是否允许重复值

* ModelAdmin.save_related(request, form, formsets, change)

Here you can do any pre- or post-save operations for objects related to the parent. Note that at this point the parent object and its form have already been saved.

* ModelAdmin.get_autocomplete_fields(request)

return a list or tuple of field names that will be displayed with an autocomplete widget as described above in the ModelAdmin.autocomplete_fields section.

* ModelAdmin.get_readonly_fields(request, obj=None)

* ModelAdmin.get_prepopulated_fields(request, obj=None)

* ModelAdmin.get_list_display(request)

* ModelAdmin.get_list_display_links(request, list_display)

* ModelAdmin.get_exclued(reqquest, obj=None)

* ModelAdmin.get_fields(request, obj=None)

* ModelAdmin.fields(request, obj=None)

* ModelAdmin.getfieldsets(request, obj=None)

* ModelAdmin.get_list_filter(request)

* ModelAdmin.get_list_select_related(request)

* ModelAdmin.get_search_fields(request)

* ModelAdmin.get_sortable_by(request)

* ModelAdmin.get_inline_instances(request, obj=None)

* ModelAdmin.get_urls()

The get_urls method on a ModelAdmin returns the URLs to be used for that ModelAdmin in the same way as a URLconf. 

* ModelAdmin.get_form(reqeust, obj=None, **kwargs)

Returns a ModelForm class for use in the admin add and change views, see add_view() and change_view().

* ModelAdmin.get_formsets_with_lines(request, obj=None)

* ModelAdmin.formfield_for_foreignkey(db_field, request, **kwargs)

* ModelAdmin.formfield_for_manytomant(db_field, request, **kwargs)

* ModelAdmin.fromfield_for_choice_field(db_field, request, **kwargs)

* ModelAdmin.get_changelist(request, **kwargs)

* ModelAdmin.get_changelist_form(request, **kwargs)

* ModelAdmin.get_changelist_formset(request, **kwargs)

* ModelAdmin.lookup_allowed(lookup, value)

* ModelAdmin.has_view_permission(request, obj=None)

* ModelAdmin.has_add_permission(request)

* ModelAdmin.has_change_permission(request, obj=None)

* ModelAdmin.has_delete_permission(request, obj=None)

* ModelAdmin.han_module_permission(request)

* ModelAdmin.get_queryset(request)

* ModelAdmin.message_user(request, message, level=meeesages.INFO, extra_tags=", fail_silently=False)

* ModelAdmin.get_paginator(request, queryset, per_page, orphans=0, allwo_empty_first_page=True)

* ModelAdmin.response_add(request, obj, post_url_continu=None)

* ModelAdmin.response_change(request, obj)

* ModelAdmin.request_delete(request, obj)

* ModelAdmin.get_changeform_inital_data(request)

* ModelAdmin.get_deleted_objects(objs, request)

#### Other methods

* ModelAdmin.add_view(request, form_url="", extra_context=None)

* ModelAdmin.change_view)request, object_id, form_url="", extra_context=None)

* ModelAdmin.changelist_view(request, extra_context=None)

* ModelAdmin.delete_view(request, object_id, extra_context=None)

* ModelAdmin.history_view(request, object_id, extra_context=None)


### ModelAdmin asset definitions

```python
class ArticleAdmin(admin.ModelAdmin):
    class Media:
        css = {
            "all": ("my_styles.css",)
        }
        js = ("my_code.js",)
```

#### jQuery

### Adding custom validation to the admin

## InlineModelAdmin objects

```python
class InlineModelAdmin
class TabularInline
class StackedInline
```

```python
from django.contrib import admin

class BookInline(admin.TabularInline):
    model = Book

class AuthorAdmin(admin.ModelAdmin):
    inlines = [
        BookInline,
    ]
```

### InlineModelAdmin options

略

### Working with a model with two or more foreign keys to the same parent model

略

### Working with many-to-may models

略

### Working with many-to-many intermediary models

略

### Using generic relation as an inline

略

## Overrding admin templates

覆盖管理模块用于生成管理站点的各种页面的许多模板相对容易。您甚至可以为特定的应用程序或特定模式覆盖其中的一些模板

 If you have customized the 'loaders' option, be sure 'django.template.loaders.filesystem.Loader' appears before 'django.template.loaders.app_directories.Loader' so that your custom templates will be found by the template loading system before those that are included with django.contrib.admin.

如果要修改 my_app 目录下全部 model  视图列表可以复制文件到 `templates/admin/my_app`

如果想要添加一个 tool 在 change list 视图仅仅为一个叫 Page 的 model。可以赋值相同文件到 `templates/admin/my_app/page` 中

### Set up your projects admin template directories

first create an admin directory in your project’s templates directory. 

### Overriding vs replacing an admin template

通常不建议替换整个模板，最好只覆盖需要更改的模板部分

### Templates which may be overridden per app or model

略

### Root and login templates

略

## AdminSite Object

一个 Django 管理站点由 `django.contrib.admin.sites.AdminSite` 的实例来表示，默认情况下，这个类的实例被创建为 `django.contrib.admin.site`，你可以用它来注册你的模型和 ModelAdmin 实例。

可以通过覆盖它来自定义默认的管理站点。

当构造 AdminSite 实例时，可以使用构造函数的 name  参数提供一个唯一的实例名。这个实例名是用来识别实例的，特别是在 reversing admin URLs。如果没有提供，将使用名人的 admin

### AdminSite attributes

* site_header
放在页面顶部的文字作为 H1 内的内容，默认：Django 管理

* site_title
页面 title 结尾处放置的文字，默认：Django 站点管理员

* site_url
管理页面顶部 查看站点 链接的 URL，默认：/，设为 None 可以删除该链接

* index_title
管理站点首页顶部展示的文字，默认：网站管理

* index_template
will be used by the admin site main index view.

* app_index_template
will be used by the admin site app index view.

* empty_value_display
用于在 change list 显示空值，默认为破折号。也可以被 ModelAdmin.empty_value_display 覆盖

* login_template
will be used by the admin site login view.

* login_form
will be used by the admin site login view.

* logout_template
Path to a custom template that will be used by the admin site logout view.

* password_change_template
will be used by the admin site password change view.

* password_change_done_template
will be used by the admin site password change done view.

### AdminSite methods

* AdminSite.each_context(request)
返回变量字典，用来放在 admin site 中每个页面的模板上下文中

默认情况下包括以下变量和值：
* site_header:Adminsite.site_header
* site_title:AdminSIte.site_title
* site_url:AdminSite.site_url
* has_permission:Admin.has_permission()
* available_apps: 当前用户可以使用的应用程序注册表中的应用程序列表。列表中的每个条目都是一个代表 application 的字典，其键如下：
	* app_label: 应用的 label
	* app_url：应用在 admin 中的 index URL
	* has_module_perms：表示是否允许当前用户显示和访问模块 index 页面的布尔值
	* models：应用程序中可用模型列表，每个 model 都是一个字典，包含以下几个键
		* object_name：model 的类名
		* name：复数名
		* perms：一个跟踪添加、更改、删除和查看权限的字典
		* admin_url：admin changelist URL for the model
		* add_url：admin URL to add a new model instance

* AdminSite.has_permission(request)
如果 HttpRequest 提供的用户邮权限查看管理员站点中至少一个页面，返回 True。默认要求 User.is_active 和 User.is_staff 为 True。

* AdminSite.register(model_or_iterable, admin_class=None, \*\*options)
admin_class 默认是 ModelAdmin。如果给定关键字参数，会作为选项应用到 admin 类

如果一个模型是抽象的，会引发 ImproperlyConfigured

如果模型已经注册，会引发 django.contrib.admin.sites.AlreadyRegistered

### Hocking AdminSite instances into your URLconf

略

### Customizing the AdminSite class

略

### Overriding the default admin site

略

### Multiple admin sites in the same URLconf

略

### Adding views to admin sites

略

### Adding a password reset feature

略

## LogEntry objects

LogEntry类跟踪通过管理接口完成的对象的添加、更改和删除。

### LogEntry attributes

* LogEntry.action_time

The date and time of the action.

* LogEntry.user

The user (an AUTH_USER_MODEL instance) who performed the action.

* LogEntry.content_type

The ContentType of the modified object

* LogEntry.object_id

The textual representation of the modified object’s primary key.

* LogEntry.object_repr

The object`s repr() after the modification.

* LogEntry.ation_flag

The type of action logged: ADDITION, CHANGE, DELETION.

* LogEntry.change_message

The detailed description of the modification. 

### LogEntry methods

* LogEntry.get_edited_object()

A shortcut that returns the referenced object.

* LogEntry.get_change_message()

## Reversing admin URLs

AdminSite

| Page                      | URL name             | Parameters                 |
| ------------------------- | -------------------- | -------------------------- |
| Index                     | index                |                            |
| Login                     | login                |                            |
| Logout                    | logout               |                            |
| Password change           | password_change      |                            |
| Password change done      | password_change_done |                            |
| i18n JavaScript           | jsi18n               |                            |
| Application index page    | app_list             | app_label                  |
| Redirect to object's page | view_on_site         | content_type_id, object_id |

ModelAdmin

| Page       | URL name                                        | Parameters |
| ---------- | ----------------------------------------------- | ---------- |
| Changelist | `{{ apple_label }}_{{ model_name }}_changelist` |            |
| Add        | `{{ apple_label }}-{{ model_name }}_add`        |            |
| History    | `{{ apple_label }}-{{ model_name }}_history`    | object_id  |
| Delete     | `{{ apple_label }}-{{ model_name }}_delete`     | object_id  |
| Change     | `{{ apple_label }}-{{ model_name }}_change`     | object_id  |

UserAdmin

| Page       | URL name                                        | Parameters |
| ---------- | ----------------------------------------------- | ---------- |
| Password change | auth_user_password_change | user_id |

## The staff_member_required decorator