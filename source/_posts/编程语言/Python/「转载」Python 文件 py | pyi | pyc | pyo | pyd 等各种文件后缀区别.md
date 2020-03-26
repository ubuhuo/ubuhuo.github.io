---
title: 「转载」Python 文件 py | pyi | pyc | pyo | pyd 等各种文件后缀区别
date: 2019/8/4
categories:
  - 编程语言
  - Python
copyright: true
---

原文：[Quant_Learner：Python文件.py||.pyi||.pyc||.pyo||.pyd等各种文件后缀区别（全面汇总）][1]

1. `.py`：这通常是您编写的输入源代码
2. `.py3`：`Python3` 脚本（`Python3` 脚本通常以 `.py` 而不是 `.py3` 结尾，很少使用）
3. `.pyc`：这是编译好的字节码。如果您导入一个模块，`Python` 将生成一个 `*.pyc` 包含字节码的文件，以便以后再次导入它更容易(也更快)。`.pyc` 二进制文件可以反编译成 `.py` 文件，反编译软件叫 `Easy Python Decompiler`
4. `.pyo`：这是在优化(-O)时创建的 `*.pyc` 文件,从 `Python 3.5` 开始，`Python` 将只使用 `pyc` 而不是 `pyo` 和 `pyc`
5. `.pyd`：这基本上是一个 `Windows DLL` 文件
6. `.pyi`：`MyPy` 存根，存根文件（`PEP 484`）
7. `.pyw `：用 `pythonw.exe` 执行的 `Windows` 的 `Python` 脚本
8. `.pyx `：将 `Cython src` 转换为 `C/C++`
9. `.pyz `：`Python` 脚本归档（`PEP 441`）（这是一个包含标准 `Python` 脚本头之后的二进制形式的压缩 `Python` 脚本（`ZIP`）的脚本）
10. `.pywz`：用于 `MS-Windows` 的 `Python` 脚本归档（`PEP 441`）（这是一个包含标准 `Python` 脚本头之后的二进制形式的压缩 `Python` 脚本（`ZIP`）的脚本）
11. `.py [cod]`：`.gitignore` 中的通配符表示该文件可能是 `.pyc, .pyo, .pyd`
12. `.rpy` : 包含应用程序或框架特定功能的 `RPython` 脚本或 `Python` 脚本
13. `.pyde` : 处理使用的 `Python` 脚本
14. `.pyp` : `Py4D Python` 插件
15. `.pyt` : `Python` 声明文件

[1]: https://blog.csdn.net/The_Time_Runner/article/details/89352464