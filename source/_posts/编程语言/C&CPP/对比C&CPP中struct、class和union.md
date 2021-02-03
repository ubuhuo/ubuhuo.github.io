---
title: 对比 C 和 CPP 中 struct class 和 union
date: 2020/11/11
categories:
  - 编程语言
  - C&CPP
tags:
  - CPP
copyright: true
---

## C struct 与 CPP struct 对比

### 定义方式

#### C

```c
// 第一种方式
typedef struct A{
  ...
}a;

struct A test;
a test1;

// 第二种方式
struct B{
  ...
};

struct B test3;
```

#### CPP

```c++
// 第一种方式
typedef struct A{
  ...
}a;

struct A test;
a test1;

// 第二种方式
struct B{
  ...
};

struct B test3;

// 第三种方式
struct C{
  ...
};

C test4;
```

### 结构体内容

CPP 中 struct 可以有成员函数（构造函数、析构函数、常规函数和虚函数）和静态成员（静态成员函数和静态成员变量）。

### 其他

CPP 中 struct 可以继承 class 和 struct，也可以被 class 和 struct 继承；

CPP 中 struct 内可以使用 public proctected private 访问属性，默认为 public，C 中 struct 访问属性默认为 public 且不可修改；

C 中 sizeof 空结构体结果为 1；
CPP 中 sizeof 空结构体结果为 0。

## CPP 中 struct 与 class 对比

1. struct 默认访问属性是 public，class 则是 private；
2. struct 继承 struct 和 class 后默认访问属性是 public；
3. class 继承 struct 和 class 后默认访问属性时 private；
4. struct 如果没有定义构造函数或虚函数则可以使用 `{}` 进行初始化：struct 使用 `{}` 进行初始化时按照数据在内存中顺序从其实地址依次初始化的，在 struct 定义虚函数后 struct 其实地址首先是虚指针，在 struct 定义构造函数后则使用构造函数进行初始化；
5. class 可用于定义模板参数，struct 不行。

## CPP 中 union 特性

1. 默认访问属性为 public；
2. 不能继承也不能被继承；
3. 所有元素（成员变量）共用一块内存，并且内存大小是成员变量中类型最大的值。

## 总结

union 主要用来节省内存空间；
struct 主要用来对某种结构进行封装；
class 主要用来对某类对象进行封装；

## 参考
1. [知乎-LWenlong：C++ union、struct 和 class][1]
2. [赵大寳：C/C++中结构体的定义以及实例化][2]
3. [拓扑梅尔-BlogUpdater：深度理解：struct和class的区别][3]
4. [CSDN-长空飞鸟：Thinking in C++: C++中关于union、struct、class的区别][4]

[1]: https://zhuanlan.zhihu.com/p/106676923
[2]: https://fuhailin.github.io/C-CPP-Structer/
[3]: https://www.topomel.com/archives/1297.html
[4]: https://blog.csdn.net/iflysoft/article/details/9232885