---
title: RESTful 笔记-1-接口设计规范
date: 2019/1/25
categories:
  - 编程思想
  - RESTful
tags:
  - RESTful 接口设计规范
copyright: true
abbrlink: 7e15b804
---

本文所有内容皆为个人根据网上文章整理思考所得并非权威指南，仅供参考。

## 1. 核心思想

万物皆资源

## 2. URI命名

### 2.1 命名规则

普遍规则：全部小写，用中划线连接。

单数复数
个人习惯：全部单数

### 2.2 HTTP 方法

常用的方法有 `GET,POST,PUT,DELETE`，此外有时还会用到 `HEAD,PATCH`和 `OPTION`。

`GET` 是查询操作，不会产生状态转移；
`DELETE` 表示删除，将资源状态转移为删除；
`POST` 表示添加一个资源，即资源状态从无转移到有;
`PUT` 和 `PATCH` 是更新操作，会将资源状态转移为请求方所期望的状态；

tip: 这里我们可以看到所有的 `HTTP` 方法所对应的都是资源状态的变化而非简单的增删改查。

### 2.3 拓展：安全、幂等、可缓存

| HTTP 方法 | 安全性 | 幂等性 |
| --- | --- | --- |
| GET | Yes | Yes |
| PUT | No | Yes |
| POST | No | No |
| HEAD | Yes | Yes |
| PATCH | No | No |
| DELETE | No | Yes |

幂等性在 `RESTful` 中的含义：同一个操作，执行一次和执行多次的效果是一样的。

## 3. 响应消息体格式

### 3.1 媒体类型与字符编码

媒体类型一般为：`JSON`
字符编码一般为：`UTF-8`

最好：
客户端请求时携带请求头 `Content-Type: application/json`
客户端请求中包含请求头 `Accept` 时它的值包含 `application/json`

### 3.2 响应信息包装与可见性

常见的两种响应信息格式：

```json
# 带包装
{
    "code": 200,
    "data": {
        "isbn": "9780321125217", 
        "name": "Domain-Driven Design"
    }
}
# 不带包装
{
    "isbn": "9780321125217", 
    "name": "Domain-Driven Design"
}
```

一般情况下我会选择不带包装。

### 3.3 响应信息统一格式

相同概念相同命名：多个表示同一概念的资源命名应保证相同；
相似概念相同数据类型：如:时间都用时间戳，是否都用 01 表示等等；
命名要符合团队思维定式：如:主键以首字母 + `id` 命名，外键以 `_id` 结尾；

## 4. 异常处理

### 4.1 状态码

`4xx` 客户端错误
`5xx` 服务端错误

### 4.2 异常响应信息

常用的异常响应信息体：

```json
{
    "code": "",
    "message": "",
    "detail": {}
}
```

`code`：必须，错误码
`message`：必须，错误信息
`detail`：非必须，错误详情

tip：异常响应信息针对的一般为 `4xx` 的错误响应，`5xx` 的错误响应应为异常日志记录，这里暂不讨论。

## 5. 版本化

如需版本化推荐使用：`/{version}/{name}/{pk}`

## 6. 鉴权

个人方案：使用 `JWT(JSON Web Token)`

实现过程：
在请求 `HTTP HEADER` 中添加 `Authorization` 信息
服务端使用中间件拦截请求 `HTTP HEADER` 中 `Authorization` 的信息并验证

## 7. 接口文档

待续…