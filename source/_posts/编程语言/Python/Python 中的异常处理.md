---
title: Python 中的异常处理
date: 2019/7/22
categories:
  - 编程语言
  - Python
tags:
  - 异常处理
copyright: true
---

## 错误和异常

目前在 `Python` 中（至少）有两种可区分的错误：**语法错误**和**异常**。

### 语法错误

**语法错误又称解析错误**，可能是在学习 Python 时最容易遇到的错误：

```python
>>> while True print('Hello world')
  File "<stdin>", line 1
    while True print('Hello world')
                   ^
SyntaxError: invalid syntax
```

### 异常

在执行时检测到的错误被称为**异常**，大多数异常并**不会被程序自动处理**，此时会显示如下所示的错误信息：

```python
>>> 10 * (1/0)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
>>> 4 + spam*3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'spam' is not defined
>>> '2' + 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: Can't convert 'int' object to str implicitly
```
 
错误信息的最后一行告诉我们程序遇到了什么类型的错误。异常有不同的类型，而其类型名称将会作为错误信息的一部分中打印出来。这一行的剩下的部分根据异常类型及其原因提供详细信息。

错误信息的前一部分以**堆栈回溯**的形式显示发生异常时的**上下文**。通常它包含列出源代码行的堆栈回溯；但是它不会显示从标准输入中读取的行。

作为异常类型打印的字符串是发生的**内置异常**的名称。对于所有内置异常都是如此，但对于用户定义的异常则不一定如此（虽然这是一个有用的规范）。标准的异常类型是内置的标识符（而不是保留关键字）。

### 内置异常

篇幅问题，请参考：[Python 中的内置异常][2]

### 异常处理

异常处理工作由「捕获」和「抛出」两部分组成。「捕获」指的是使用 `try ... except` 包裹特定语句，妥当的完成错误流程处理。而恰当的使用 `raise` 主动「抛出」异常，更是优雅代码里必不可少的组成部分。

#### 捕获

##### try 语句的工作原理

1. 首先，执行 `try` 子句（`try` 和 `except` 关键字之间的（多行）语句）。
2. 如果没有异常发生，则跳过 `except` 子句并完成 `try` 语句的执行。
3. 如果在执行 `try` 子句时发生了异常，则**跳过**该子句中剩下的部分。然后，如果异常的类型和 `except` 关键字后面的异常匹配，则执行 `except` 子句 ，然后**继续**执行 `try` 语句之后的代码。
4. 如果发生的异常和 `except` 子句中指定的异常不匹配，则将其传递到外部的 `try` 语句中；如果没有找到处理程序，则它是一个**未处理异常**，执行将**停止**并显示错误的消息。


一个 try 语句可能有多个 except 子句，以指定不同异常的处理程序，但最多会执行一个处理程序。处理程序只处理相应的 `try` 子句中发生的异常，而不处理同一 `try` 语句内其他处理程序中的异常。一个 `except` 子句可以将多个异常命名为带括号的元组，例如:

```python
... except (RuntimeError, TypeError, NameError):
...     pass
```

如果发生的异常和 `except` 子句中的类是同一个类或者是它的基类，则异常和 `except` 子句中的类是**兼容的**（但反过来则不成立）。例如，下面的代码将依次打印 `B, C, D`

```python
class B(Exception):
    pass

class C(B):
    pass

class D(C):
    pass

for cls in [B, C, D]:
    try:
        raise cls()
    except D:
        print("D")
    except C:
        print("C")
    except B:
        print("B")
```

请注意如果 `except` 子句被颠倒（把 `except B` 放到第一个），它将打印 `B，B，B` --- 即第一个匹配的 `except` 子句被触发。

**最后的** `except` 子句可以省略异常名，以用作通配符。但请谨慎使用，因为**以这种方式很容易掩盖真正的编程错误**！它还可用于打印错误消息，然后重新引发异常（同样允许调用者处理异常）。

`try ... except` 语句有一个可选的 `else` 子句，在使用时必须放在所有的 `except` 子句后面。对于在 `try` 子句不引发异常时必须执行的代码来说很有用。

使用 `else` 子句比向 `try` 子句添加额外的代码要好，因为它避免了意外捕获由 `try ... except` 语句保护的代码未引发的异常。

异常处理程序不仅处理 `try` 子句中遇到的异常，还处理 `try` 子句中调用（即使是间接地）的函数内部发生的异常。

##### 异常参数

发生异常时，它可能具有关联值，也称为**异常参数**。参数的存在和类型**取决于异常类型**。

`except` 子句可以在异常名称后面指定一个变量。这个变量和一个异常实例绑定，它的参数存储在 `instance.args` 中。为了方便起见，异常实例定义了 `__str__()`，因此可以直接打印参数而无需引用 `.args`。也可以在抛出之前首先实例化异常，并根据需要向其添加任何属性。

```python
>>> try:
...     raise Exception('spam', 'eggs')
... except Exception as inst:
...     print(type(inst))    # the exception instance
...     print(inst.args)     # arguments stored in .args
...     print(inst)          # __str__ allows args to be printed directly,
...                          # but may be overridden in exception subclasses
...     x, y = inst.args     # unpack args
...     print('x =', x)
...     print('y =', y)
...
<class 'Exception'>
('spam', 'eggs')
('spam', 'eggs')
x = spam
y = eggs
```

如果异常有参数，则它们将作为未处理异常的消息的最后一部分（详细信息）打印。

#### 抛出

`raise` 语句允许程序员强制发生指定的异常。例如:

```python
>>> raise NameError('HiThere')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: HiThere
```

`raise` 唯一的参数就是要抛出的异常。这个参数必须是一个异常**实例**或者是一个异常**类**（派生自 `Exception` 的类）。如果传递的是一个异常类，它将通过调用没有参数的构造函数来隐式实例化:

```python
raise ValueError  # raise ValueError() 的简写
```

如果你需要确定是否引发了异常但不打算处理它，则可以使用更简单的 `raise` 语句形式重新引发异常：

```python
>>> try:
...     raise NameError('HiThere')
... except NameError:
...     print('An exception flew by!')
...     raise
...
An exception flew by!
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
NameError: HiThere
```

### 用户自定义异常

程序可以通过创建新的异常类来命名它们自己的异常。异常通常应该直接或间接地从 `Exception` 类派生。

可以定义异常类，它可以执行任何其他类可以执行的任何操作，但通常保持简单，通常只提供许多属性，这些属性允许处理程序为异常提取有关错误的信息。在创建可能引发多个不同错误的模块时，通常的做法是为该模块定义的异常创建基类，并为不同错误条件创建特定异常类的子类。

大多数异常都定义为名称以 `Error` 结尾，类似于标准异常的命名。

许多标准模块定义了它们自己的异常，以报告它们定义的函数中可能出现的错误。

### 定义清理操作

`try` 语句有另一个可选子句，用于定义必须在所有情况下执行的清理操作。

`finally` 子句总会在离开 `try` 语句前被执行，无论是否发生了异常。当在 `try` 子句中发生了异常且尚未被 `except` 子句处理（或者它发生在 `except` 或 `else` 子句中）时，它将在 `finally` 子句执行后被重新抛出。当 `try` 语句的任何其他子句通过 `break, continue, return` 语句离开时，`finally` 也会在「离开之前」被执行。

在实际应用程序中，`finally` 子句对于**释放外部资源（例如文件或者网络连接）**非常有用，无论是否成功使用资源。

## 进行异常处理时的小技巧

### 传递异常

有时我们会在捕捉到一个异常后重新引发它（传递异常），实现起来很简单，使用不带参数的 `raise` 语句即可，例如：

```python
def f1():
    print(1/0)

def f2():
    try:
        f1()
    except Exception as e:
        print('something worng')
        raise

f2()
```

```
# 运行结果
something worng
Traceback (most recent call last):
  File "/Users/ryoma/Desktop/project/learn/learn_python/python_exception.py", line 11, in <module>
    f2()
  File "/Users/ryoma/Desktop/project/learn/learn_python/python_exception.py", line 6, in f2
    f1()
  File "/Users/ryoma/Desktop/project/learn/learn_python/python_exception.py", line 2, in f1
    print(1/0)
ZeroDivisionError: division by zero
```

### 使用内置的语法规范代替 try/except

`Python` 本身提供了很多语法范式简化了异常处理，例如：
1. `for` 语句利用 `Stoplteration` 异常来结束循环的
2. `with` 语句在打开文件后会在操作结束后（无论是否正常结束）会自动关闭文件句柄
3. 使用 `getattr()` 函数获取对象中的不确定属性

以上这些都是 `Python` 自身封装好的语法范式，在处理这些事件的时候应避免使用 `try/except/finally` 的思维来处理。

## 异常处理的三个好习惯

### 只做精确的异常捕获

在 `Python` 中使用异常捕获的目的并不是使自己写的代码不出现任何异常，而是在可能因外部力量而出错的部分进行预防，例如对用户输入部分进行异常捕获。

在 `Python` 中使用异常捕获时应捕获尽可能精确的异常类型，而不是模糊的 `Exception`，因为模糊的捕获 `Exception` 有时会导致本该被显示的有用的错误信息被自定义的错误信息「吃」掉。

另外，使自己写的代码不出现任何异常的最好方法是规范的代码书写习惯。

### 别让异常破坏代码抽象分层的一致性

很多场景下我们会对异常类进行包装，方便在产生已知异常时自定义错误信息，这样做能大大提高后续的编码效率，但在使用时如果没有做好分层处理很容易击穿代码的抽象分层逻辑，具体案例请参考 [Python 工匠： 异常处理的三个好习惯][3]。

为了避免因为使用错误的异常处理方式导致代码的抽象分层逻辑被打破：
1. 让模块只调用与当前抽象层级一致的异常类，既不能高于当前抽象层级，也不能低于当前抽象层级
2. 在需要跨层级调用异常类时应通过异常包装与转换的方法进行，而不是直接跨层级调用异常类

### 异常处理不应该喧宾夺主

当非异常处理逻辑代码中存在大量异常处理操作时，很容易出现因异常处理的逻辑代码太多而扰乱核心的逻辑代码。

```python
# 代码来自：Python 工匠：异常处理的三个好习惯
def upload_avatar(request):
    """用户上传新头像"""
    try:
        avatar_file = request.FILES['avatar']
    except KeyError:
        raise error_codes.AVATAR_FILE_NOT_PROVIDED

    try:
       resized_avatar_file = resize_avatar(avatar_file)
    except FileTooLargeError as e:
        raise error_codes.AVATAR_FILE_TOO_LARGE
    except ResizeAvatarError as e:
        raise error_codes.AVATAR_FILE_INVALID

    try:
        request.user.avatar = resized_avatar_file
        request.user.save()
    except Exception:
        raise error_codes.INTERNAL_SERVER_ERROR
    return HttpResponse({})
```

此时我们可以使用 `Python` 中的 **上下文管理器（context manager）**配合 `with` 语句简化异常处理过程。

```python
# 代码来自：Python 工匠：异常处理的三个好习惯
class raise_api_error:
    """captures specified exception and raise ApiErrorCode instead
    :raises: AttributeError if code_name is not valid
    """
    def __init__(self, captures, code_name):
        self.captures = captures
        self.code = getattr(error_codes, code_name)

    def __enter__(self):
        # 刚方法将在进入上下文时调用
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        # 该方法将在退出上下文时调用
        # exc_type, exc_val, exc_tb 分别表示该上下文内抛出的
        # 异常类型、异常值、错误栈
        if exc_type is None:
            return False

        if exc_type == self.captures:
            raise self.code from exc_val
        return False
```

在上面的代码里，定义了一个名为 `raise_api_error` 的上下文管理器，它在进入上下文时什么也不做。但是在退出上下文时，会判断当前上下文中是否抛出了类型为 `self.captures` 的异常，如果有，就用 `APIErrorCode` 异常类替代它。

使用该上下文管理器后，上面臃肿的 `upload_avatar` 函数变得更清晰简洁：

```python
# 代码来自：Python 工匠：异常处理的三个好习惯
def upload_avatar(request):
    """用户上传新头像"""
    with raise_api_error(KeyError, 'AVATAR_FILE_NOT_PROVIDED'):
        avatar_file = request.FILES['avatar']

    with raise_api_error(ResizeAvatarError, 'AVATAR_FILE_INVALID'),\
            raise_api_error(FileTooLargeError, 'AVATAR_FILE_TOO_LARGE'):
        resized_avatar_file = resize_avatar(avatar_file)

    with raise_api_error(Exception, 'INTERNAL_SERVER_ERROR'):
        request.user.avatar = resized_avatar_file
        request.user.save()
    return HttpResponse({})
```

## 参考

感谢参考文章的作者（译者）

[Python 3.7.4 中文文档-错误和异常][1]
[Python 工匠： 异常处理的三个好习惯][3]
[地球的外星人君:一文掌握 Python 异常处理的所有知识点][4]


[1]: https://docs.python.org/zh-cn/3/tutorial/errors.html
[2]: https://wiki.blanc.site/archives/4007339e.html
[3]: https://www.zlovezl.cn/articles/three-rituals-of-exceptions-handling/
[4]: https://zhuanlan.zhihu.com/p/33454825?group_id=941986895471882240