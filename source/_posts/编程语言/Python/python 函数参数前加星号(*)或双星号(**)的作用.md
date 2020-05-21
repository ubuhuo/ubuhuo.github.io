---
title: python 函数参数前加星号(*)或双星号(**)的作用
date: 2020/05/21
categories:
  - 编程语言
  - Python
copyright: true
---

## 概述

`*args` 和 `**kwargs` 在 `Python` 函数定义中非常常见，一般函数定义了这两个参数之后就能允许传入任意数量的参数，在 [Python 官方文档][1] 中也有提到这部分内容。

总的来说：

1. 在函数定义时：
    1. 使用单星号（`*`）会将所有参数放入一个元组（`tuple`）中供函数调用——**对应例一**。
    2. 使用双星号（`**`）会将所有关键字参数放入一个字典（`dict`）中供函数使用——**对应例二**。
2. 在函数调用时：
    1. 在列表，元组或集合类型的参数前加单星号（`*`）会把该容器中所有的元素解析成**位置参数**传入函数——**对应例三**。
    2. 在字典类型的参数前加单星号（`*`）会把该字典的键解析成**位置参数**传入函数——**对应例四**。
    3. 在字典类型的参数前加双星号（`**`）会把该字典的键值对解析成**关键字参数**传入函数——**对应例五**。

## 例子

### 例一

```python
def foo(*a):
    print(a)

foo(1,2,3)

# result: ([1, 2, 3],)
```

### 例二

```python
def foo(**a):
    print(a)

foo(a=1, b=2, c=3)

# result: {'a': 1, 'b': 2, 'c': 3}
```

### 例三

```python
def foo(a, b, c):
    print(f"a: {a}")
    print(f"b: {b}")
    print(f"c: {c}")

d = [1,2,3]

foo(*d)

"""
result:
a: 1
b: 2
c: 3
"""
```

### 例四

```python
def foo(a, b=1, c=1):
    print(f"a: {a}")
    print(f"b: {b}")
    print(f"c: {c}")

d = {"b": 2, "c": 3}

foo(1, *d)

"""
注意，这里是将字典的键解析成未知参数，而非值
result:
a: 1
b: b
c: c
"""
```

### 例五

```python
def foo(a, b=1, c=1):
    print(f"a: {a}")
    print(f"b: {b}")
    print(f"c: {c}")

d = {"b": 2, "c": 3}

foo(1, **d)

"""
可以将这一组结果和例四的结果进行对比
result:
a: 1
b: 2
c: 3
"""
```

### 例一变形

```python
def foo(a, *b):
    print(f"a: {a}")
    print(f"b: {b}")

foo(1, 2, 3, 4, 5)

"""
result:
a: 1
b: (2, 3, 4, 5)
"""
```

### 例二变形

```python
def foo(a, b=1, **c):
    print(f"a: {a}")
    print(f"b: {b}")
    print(f"c: {c}")

foo(1, 2, 3)
"""
报错原因：双星号的 c 只能解析关键字参数
result:
TypeError: foo() takes from 1 to 2 positional arguments but 3 were given
"""

foo(1, 2, d=3)
"""
result:
a: 1
b: 2
c: {'d': 3}
"""

foo(1, 2, a=1)
"""
报错原因：传入的关键字参数和原函数的位置参数重名
result:
TypeError: foo() got multiple values for argument 'a'
"""

foo(1, 2, e=4, f=5, g=6)
"""
result:
a: 1
b: 2
c: {'e': 4, 'f': 5, 'g': 6}
"""
```

其实每一个例子都可以进行拓展变形，而且各个例子还可以混合使用，因为可能性太多，我就不一一列举了。

## 参考

1. [知乎-按部就班：Python 函数中 * 和 ** 的内涵究竟是什么呢？][2]
2. [stackoverflow: What does ** (double star/asterisk) and * (star/asterisk) do for parameters?][3]

[1]: https://docs.python.org/zh-cn/dev/tutorial/controlflow.html#arbitrary-argument-lists
[2]: https://www.zhihu.com/question/265519629/answer/295435756
[3]: https://stackoverflow.com/questions/36901/what-does-double-star-asterisk-and-star-asterisk-do-for-parameters