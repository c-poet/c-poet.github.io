+++
draft = false
author = "CPoet"
title = "最新Linux程序重启、开启关闭开机启动功能"
date = "2019-03-17T00:02:01+08:00"
description = ""
tags = ["Linux"]
categories = [
    "linux",
]
+++
## 前言
在开发的过程中，我们都需要用到很多的开发环境，但是在平时开发环境就没有必要运行起来，因此关闭程序的开机启动是很有必要的。前期AS也是在网上搜索了很多关于**Linux下关闭程序自启动**的方法，但是都失败了。后来在学习Linux的时候，我才知道网上的方法适用于使用system v into启动的Linux，而现在大多数的Linux衍生版已经使用systemd启动。因此在管理程序的时候还是有一定的区别。
下面我就列出两种启动加载下的命令，可根据自己使用Linux的情况使用命令。

## Systemd
### 启动服务
```cmd
systemctl start servicename.service
```
### 关闭服务
```cmd
systemctl stop servicename.service
```
### 重启服务
```cmd
systemctl restart sevicename.service
```
### 重新加载配置文件
```cmd
systemctl reload sevicename.service
```
### 查看服务状态
```cmd
systemctl status revicename.service
```
### 开机自启动
```cmd
systemctl enable revicename.service
```
### 关闭开机自启动
```cmd
systemctl disable revicename.service
```
## System V init
### 启动服务
```cmd
service servicename start
```
### 关闭服务
```cmd
service servicename stop
```
### 重启服务
```cmd
service servicename restart
```
### 重新加载配置文件
```cmd
service servicename reload
```
### 查看服务状态
```cmd
service servicename status
```
### 开机自启动
```cmd
chkconfig servicename on
```
### 关闭开机自启动
```cmd
chkconfig servicename off
```

## END
