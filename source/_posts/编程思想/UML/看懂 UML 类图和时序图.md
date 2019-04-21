---
title: 看懂 UML 类图和时序图
date: 2019/04/08
categories:
  - 编程思想
  - UML
tags:
  - UML
  - 看懂 UML 类图和时序图
copyright: true
---

## 什么是 UML

`Unified Modeling Language`，统一建模语言，简称：`UML`

> UML是一种开放的方法，用于说明、可视化、构建和编写一个正在开发的、面向对象的、软件密集系统的制品的开放方法
> 
> 来自 [维基百科][1]

> 它是一种由一整套图表组成的标准化建模语言，用于帮助系统开发人员阐明，展示，构建和记录软件系统的产出
> 
> 来自 [VisualParadigm Wiki][2]

## UML 概述

`UML` 图表可大致分为结构性图表和行为性图表两种。

结构性图表显示了系统在不同抽象层次和实现层次上的静态结构以及它们之间的相互关系。

结构性图表中的元素表示系统中具意义的概念，可能包括抽象的、现实的和實作的概念。

结构性图表有七种类型：

1. 类图 (`Class Diagram`)
2. 组件图 (`Component Diagram`)
3. 部署图 (`Deployment Diagram`)
4. 对象图 (`Object Diagram`)
5. 包图 (`Package Diagram`)
6. 复合结构图 (`Composite Structure Diagram`)
7. 轮廓图 (`Profile Diagram`)

行为性图表显示了系统中对象的动态行为 ，可用以表达系统随时间的变化。行为性图表有七种类型：

1. 用例图 (`Use Case Diagram`)
2. 活动图 (`Activity Diagram`)
3. 状态机图 (`State Machine Diagram`)
4. 序列图 (`Sequence Diagram`)
5. 通訊圖 (`Communication Diagram`)
6. 交互概述图 (`Interaction Overview Diagram`)
7. 时序图 (`Timing Diagram`)

## 类图

### 概述

类图是面向对象式的建模。一般用于概念建模的系统分类的应用程序，并可将模型建模转译成代码。

![类图举例](https://img.blanc.site//wiki/img/20190408230841.png)

* 最上面是类名称
* 中间部分包含类的属性
* 底部部分包含类的方法

### 成员可见性

在各成员的名字之前表明下列符号以指定其的可见性：

| + | 公共 |
| --- | --- |
| - | 私有 |
| # | 保護（即對子類可見） |
| ~ | 包（即對包内其他成員可見） |
| / | 推導（即由其他屬性推導得出，不需要直接給定其值） |
| _ | 靜態 |

### 类之间的关系

![类之间的关系](https://img.blanc.site//wiki/img/20190408232154.jpg)


* 车的类图结构为 `<<abstract>>`，表示车是一个**抽象类**；
* 它有两个继承类：小汽车和自行车；它们之间的关系为**实现关系**，使用**带空心箭头的虚线表示**；
* 小汽车为与 `SUV` 之间也是**继承解构**，它们之间的关系为**泛化关系**，使用**带空心箭头的实线表示**；
* 小汽车与发动机之间是**组合关系**，使用**带实心箭头的实线表示**；
* 学生与班级之间是**聚合关系**，使用**带空心箭头的实线表示**；
* 学生与身份证之间为**关联关系**，使用**一根实线表示**；
* 学生上学需要用到自行车，与自行车是一种**依赖关系**，使用**带箭头的虚线表示**；

#### 泛化关系(generalization)

类的**继承结构**表现在 `UML` 中为：泛化(`generalize`)与实现(`realize`)：



[1]: https://zh.wikipedia.org/wiki/%E7%BB%9F%E4%B8%80%E5%BB%BA%E6%A8%A1%E8%AF%AD%E8%A8%80
[2]: https://www.visual-paradigm.com/cn/guide/uml-unified-modeling-language/what-is-uml/