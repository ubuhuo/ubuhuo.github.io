---
title: Kong Admin API 汇总
date: 2019/11/21
categories:
  - 软件使用
  - kong
tags:
  - Kong Admin API
copyright: true
---

## Service

### 配置一个新的服务
POST
http://localhost:8001/services
name 服务的名字
url 服务的地址

## Route

### 给已经配置的服务添加路由
POST
http://localhost:8001/services/{name}/routes
hosts[] 路由地址

## Plugin

### 给已经配置的服务添加插件
POST
http://localhost:8001/services/example-service/plugins/
name 插件名称

## Consumer

### 添加一个用户
POST
http://localhost:8001/consumers/
username 用户名

