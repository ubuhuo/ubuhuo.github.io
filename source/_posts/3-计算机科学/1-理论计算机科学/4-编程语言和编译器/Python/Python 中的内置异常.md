---
title: Python 中的内置异常
date: 2019/7/22
categories:
  - 3-计算机科学
  - 1-理论计算机科学
  - 4-编程语言和编译器
  - Python
tags:
  - 内置异常
copyright: true
---

基于 [Python 3.7.4 中文文档-内置异常][1]

在 `Python` 中，所有异常必须为一个派生自 `BaseException` 的类的实例。

## 基类

* 以下异常主要被用作其他异常的基类

### BaseException

所有内置异常的基类。 它不应该被用户自定义类直接继承 (`Python` 官方鼓励程序员从 `Exception` 类或它的某个子类而不是从 `BaseException` 来派生新的异常)。 如果在此类的实例上调用 `str()`，则会返回实例的参数表示，或者当没有参数时返回空字符串。

### Exception

所有内置的**非系统退出类异常**都派生自此类。 所有用户自定义异常也应当派生自此类。

### ArithmeticError

用于派生针对各种算术类错误而引发的内置异常: `OverflowError, ZeroDivisionError, FloatingPointError`。

### BufferError

与**缓冲区**相关的操作无法执行时将被引发。

### LookupError

用于派生**映射或序列所使用的键或索引无效时引发的异常**: `IndexError, KeyError`。 

可以通过 `codecs.lookup()` 来直接引发。

## 具体异常

* 以下异常属于经常被引发的异常

### AssertionError

`assert` 语句失败时将被引发。

### AttributeError

**属性引用**或**赋值**失败时将被引发。

一个对象根本不支持属性引用或属性赋值时则将引发 `TypeError`。

### EOFError

`input()` 函数未读取任何数据即达到文件结束条件 (`EOF`) 时将被引发。 

另外，`io.IOBase.read()` 和 `io.IOBase.readline()` 方法在遇到 `EOF` 返回一个空字符串。

### FloatingPointError

目前未被使用。

### GeneratorExit

当一个 `generator` 或 `coroutine` 被关闭时将被引发。

它直接继承自 `BaseException` 而不是 `Exception`，因为从技术上来说它并不是一个错误。

### ImportError

`import` 语句尝试加载模块遇到麻烦时将被引发。 

`from ... import` 中的 `from list` 存在无法找到的名称时也会被引发。

### ModuleNotFoundError

`ImportError` 的子类，当一个模块无法被定位时将由 `import` 引发。 

在 `sys.modules` 中找到 `None` 时也会被引发。

### IndexError

序列抽取超出范围时将被引发。

**切片索引会被静默截短到允许的范围**；如果指定索引不是整数则 `TypeError` 会被引发。

### KeyError

在现有键集合中找不到指定的映射（字典）键时将被引发。

### KeyboardInterrupt

用户按下中断键 (通常为 `Control-C` 或 `Delete`) 时将被引发。 在执行期间，会定期检测中断信号。 该异常继承自 `BaseException` 以确保不会被处理 `Exception` 的代码意外捕获，这样可以**避免退出解释器**。

### MemoryError

一个操作耗尽内存但情况仍可（通过删除一些对象）进行挽救时将被引发。 关联的值是一个字符串，指明是哪种（内部）操作耗尽了内存。 请注意由于底层的内存管理架构（`C` 的 `malloc()` 函数），解释器也许并不总是能够从这种情况下完全恢复；但它毕竟可以引发一个异常，这样就能打印出栈回溯信息，以便找出导致问题的失控程序。

### NameError

某个局部或全局名称未找到时将被引发。 此异常仅用于**非限定名称**。 关联的值是一条错误信息，其中包含未找到的名称。

### NotImplementedError

此异常派生自 `RuntimeError`。 在用户自定义的基类中，抽象方法应当在其要求所派生类重载该方法，或是在其要求所开发的类提示具体实现尚待添加时引发此异常。

### OSError

**一个系统函数返回系统相关的错误**时将被引发，此类错误包括 `I/O` 操作失败例如「文件未找到」或「磁盘已满」等（不包括非法参数类型或其他偶然性错误）。

### OverflowError

算术运算的结果大到无法表示时将被引发。 这对整数来说不可能发生（宁可引发 `MemoryError` 也不会放弃尝试）。 但是出于历史原因，有时也会在整数超出要求范围的情况下引发 `OverflowError`。 因为在 `C` 中缺少对浮点异常处理的标准化，大多数浮点运算都不会做检查。

### RecursionError

此异常派生自 `RuntimeError`。 它会在**解释器检测发现超过最大递归深度**时被引发。

### ReferenceError

此异常将在使用 `weakref.proxy()` 函数所创建的弱引用来访问该引用的某个已被作为垃圾回收的属性时被引发。 

### RuntimeError

检测到一个不归属于任何其他类别的错误时将被引发。关联的值是一个指明究竟发生了什么问题的字符串。

### StopIteration

由内置函数 `next()` 和 `iterator` 的 `__next__()` 方法所引发，用来表示该迭代器不能产生下一项。

该异常对象只有一个属性 `value`，它在构造该异常时作为参数给出，默认值为 `None`。

当一个 `generator` 或 `coroutine` 函数返回时，将引发一个新的 `StopIteration` 实例，函数返回的值将被用作异常构造器的 `value` 形参。

如果某个生成器代码直接或间接地引发了 `StopIteration`，它会被转换为 `RuntimeError `(并将 `StopIteration` 保留为导致新异常的原因)。

### StopAsyncIteration

必须由一个 `asynchronous iterator` 对象的 `__anext__()` 方法来引发以停止迭代操作。

### SyntaxError

**解析器遇到语法错误时**将被引发。 可以发生在 `import` 语句，对内置函数 `exec()` 或 `eval()` 的调用，或者读取原始脚本或标准输入（也包括交互模式）的时候。

### IndentationError

与不正确的缩进相关的语法错误的基类。 是 `SyntaxError` 的一个子类。

### TabError

缩进包含对制表符和空格符不一致的使用时将被引发。 是 `IndentationError` 的一个子类。

### SystemError

解释器发现内部错误，但情况看起来尚未严重到要放弃所有希望时将被引发。关联的值是一个指明发生了什么问题的字符串（表示为低层级的符号）。

### SystemExit

此异常由 `sys.exit()` 函数引发。 它继承自 `BaseException` 而不是 `Exception` 以确保不会被处理 `Exception` 的代码意外捕获。 这允许此异常正确地向上传播并导致解释器退出。 如果它未被处理，则 `Python` 解释器就将退出；不会打印任何栈回溯信息。构造器接受的可选参数与传递给 `sys.exit()` 的相同。 如果该值为一个整数，则它指明系统退出状态码（会传递给 `C` 的 `exit()` 函数）；如果该值为 `None`，则退出状态码为零；如果该值为其他类型（例如字符串），则会打印对象的值并将退出状态码设为一。

### TypeError

一个操作或函数被应用于类型不适当的对象时将被引发。 关联的值是一个字符串，给出有关类型不匹配的详情。

### UnboundLocalError

在函数或方法中对某个局部变量进行引用，但该变量并未绑定任何值时将被引发。此异常是 `NameError` 的一个子类。

### UnicodeError

当发生与 `Unicode` 相关的编码或解码错误时将被引发。 此异常是 `ValueError` 的一个子类。

### UnicodeEncodeError

在编码过程中发生与 `Unicode` 相关的错误时将被引发。 此异常是 `UnicodeError` 的一个子类。

### UnicodeDecodeError

当在解码过程中发生与 `Unicode` 相关的错误时将被引发。 此异常是 `UnicodeError` 的一个子类。

### UnicodeTranslateError

在转写过程中发生与 `Unicode` 相关的错误时将被引发。 此异常是 `UnicodeError` 的一个子类。

### ValueError

操作或函数接收到具有正确类型但值不适合的参数，并且情况不能用更精确的异常例如 `IndexError` 来描述时将被引发。

### ZeroDivisionError

除法或取余运算的第二个参数为零时将被引发。 关联的值是一个字符串，指明操作数和运算的类型。

## 版本兼容

* 下列异常被保留以与之前的版本相兼容；从 `Python 3.3` 开始，它们都是 `OSError` 的别名。

### EnvironmentError¶

### IOError

### WindowsError

限在 `Windows` 中可用。

## OS 异常

* 下列异常均为 ·OSError· 的子类，它们将根据系统错误代码被引发。

### BlockingIOError

一个操作会被某个设置为非阻塞操作的对象（例如套接字）所阻塞时将被引发。 对应于 `errno EAGAIN, EALREADY, EWOULDBLOCK, EINPROGRESS`。

### ChildProcessError

当一个子进程上的操作失败时将被引发。 对应于 `errno ECHILD`。

### ConnectionError

与连接相关问题的基类。

其子类有 `BrokenPipeError, ConnectionAbortedError, ConnectionRefusedError, ConnectionResetError`。

### BrokenPipeError

试图写入另一端已被关闭的管道，或是试图写入已关闭写入的套接字时将被引发。 对应于 `errno EPIPE, ESHUTDOWN`。

### ConnectionAbortedError

连接尝试被对端中止时将被引发。 对应于 `errno ECONNABORTED`。

### ConnectionRefusedError

连接尝试被对端拒绝时将被引发。 对应于 `errno ECONNREFUSED`。

### ConnectionResetError

连接被对端重置时将被引发。 对应于 `errno ECONNRESET`。

### FileExistsError

试图创建一个已存在的文件或目录时将被引发。 对应于 `errno EEXIST`。

### FileNotFoundError

所请求的文件或目录不存在时将被引发。 对应于 `errno ENOENT`。

### InterruptedError

系统调用被输入信号中断时将被引发。 对应于 `errno EINTR`。

### IsADirectoryError

请求对一个目录执行文件操作 (例如 `os.remove()`) 将被引发。 对应于 `errno EISDIR`。

### NotADirectoryError

请求对一个非目录对象执行目录操作 (例如 `os.listdir()`) 时将被引发。 对应于 `errno ENOTDIR`。

### PermissionError

在没有足够操作权限的情况下试图执行某个操作时将被引发 —— 例如缺少文件系统权限。 对应于 `errno EACCES, EPERM`。

### ProcessLookupError

给定的进程不存在时将被引发。 对应于 `errno ESRCH`。

### TimeoutError

一个系统函数发生系统级超时的情况下将被引发。 对应于 `errno ETIMEDOUT`。

## 警告

* 以下异常被用作警告类别。

### Warning

警告类别的基类。

###　UserWarning

用户代码所产生警告的基类。

### DeprecationWarning

如果所发出的警告是针对其他 `Python` 开发者的，则以此作为与已弃用特性相关警告的基类。

### PendingDeprecationWarning

对于已过时并预计在未来弃用，但目前尚未弃用的特性相关警告的基类。

### SyntaxWarning

与模糊的语法相关的警告的基类。

### RuntimeWarning

与模糊的运行时行为相关的警告的基类。

### FutureWarning

如果所发出的警告是针对以 `Python` 所编写应用的最终用户的，则以此作为与已弃用特性相关警告的基类。

### ImportWarning

与在模块导入中可能的错误相关的警告的基类。

### UnicodeWarning

与 `Unicode` 相关的警告的基类。

### BytesWarning

与 `bytes` 和 `bytearray` 相关的警告的基类。

### ResourceWarning

与资源使用相关的警告的基类。 会被默认的警告过滤器忽略。

## 异常层次结构

内置异常的类层级结构如下：

```
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration
      +-- StopAsyncIteration
      +-- ArithmeticError
      |    +-- FloatingPointError
      |    +-- OverflowError
      |    +-- ZeroDivisionError
      +-- AssertionError
      +-- AttributeError
      +-- BufferError
      +-- EOFError
      +-- ImportError
      |    +-- ModuleNotFoundError
      +-- LookupError
      |    +-- IndexError
      |    +-- KeyError
      +-- MemoryError
      +-- NameError
      |    +-- UnboundLocalError
      +-- OSError
      |    +-- BlockingIOError
      |    +-- ChildProcessError
      |    +-- ConnectionError
      |    |    +-- BrokenPipeError
      |    |    +-- ConnectionAbortedError
      |    |    +-- ConnectionRefusedError
      |    |    +-- ConnectionResetError
      |    +-- FileExistsError
      |    +-- FileNotFoundError
      |    +-- InterruptedError
      |    +-- IsADirectoryError
      |    +-- NotADirectoryError
      |    +-- PermissionError
      |    +-- ProcessLookupError
      |    +-- TimeoutError
      +-- ReferenceError
      +-- RuntimeError
      |    +-- NotImplementedError
      |    +-- RecursionError
      +-- SyntaxError
      |    +-- IndentationError
      |         +-- TabError
      +-- SystemError
      +-- TypeError
      +-- ValueError
      |    +-- UnicodeError
      |         +-- UnicodeDecodeError
      |         +-- UnicodeEncodeError
      |         +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
           +-- ResourceWarning
```

## 参考

[Python 3.7.4 中文文档-内置异常][1]

[1]: https://docs.python.org/zh-cn/3/library/exceptions.html