+++
draft = false
author = "CPoet"
title = "Linux服务器统一应用服务管理和统一应用日志管理"
date = "2022-07-14T20:53:50+08:00"
description = "Linux环境下对Java应用实现统一应用管理和应用日志管理的方法"
tags = ["Linux", "Service", "Java", "Spring Boot"]
categories = [
    "linux",
]
+++

## 应用服务
### 配置方式
在Linux服务器中，应用服务可以通过Linux的“服务管理器”进行统一管理。在应用部署时只需要在`/etc/systemd/system`或`/etc/systemd/user`目录下编写如下格式的service文本即可将服务交由`systemctl`管理。

```shell
# demo.service
[Unit]
# 服务描述		
Description=演示示例服务

[Service]
# 定义执行的命令
ExecStart=java -jar /opt/*.jar
# 指定执行命令的用户
User=deamon
```

> **注意：** 建议使用`限权的用户`（例如：deamon）来启动应用服务以提升系统安全性。

### 常用命令
> 使用systemctl命令时需要进行提权，可以使用`su`命令切换到高权限用户或者使用`sudo`命令进行提权。

```shell
# 启动服务
systemctl start [demo.service]
# 停止服务
systemctl stop [demo.service]
# 重启服务
systemctl restart [demo.service]
# 服务状态
systemctl status [demo.service]
# 开机自启
systemctl enable [demo.service]
# 关闭自启
systemctl disable [demo.service]
```
### 补充说明
多数情况下都是通过**应用日志**来排查应用问题，但是存在极少数的非运行时问题，比如：*应用的路径配置不正确*。这种问题可以通过`systemctl status`进行初步问题的排查，更多日志信息可以通过监听`/var/log/messages`文件来获取。
```shell
# eg: 查看服务日志
sudo tail -900f /var/log/messages
```
## 应用日志
### 要求说明
统一应用日志管理几点要求：
1. 统一输出目录；
2. 统一日志格式规范（例如：分warn级别和info级别的日志）；
3. 日志自动压缩和自动清理。

> 统一日志目录和格式规范的话，对于运维人员和开发人员都可以快速的提取和分析日志。（就算应用不是自己部署的情况下，也可以在约定的目录找到服务运行日志）。很多情况下，日志是帮助运维、开发人员分析现有问题的工具，那么历史日志的价值就会很低，因此建议保留15至30天的日志即可。

### logback配置示例
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
  <!--读取SpringBoot中的配置-->
  <!-- 日志输出路径 -->
  <springProperty scope="context" name="logRoot" source="logging.file.path"/>
  <!-- 应用名称，作为日志文件的特征符号 -->
  <springProperty scope="context" name="logModule" source="spring.application.name"/>
  
  <!-- 警告及以上级别的日志 -->
  <appender name="FILE_WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
      <level>WARN</level>
      <onMatch>ACCEPT</onMatch>
      <onMismatch>DENY</onMismatch>
    </filter>
    <file>${logRoot}/${logModule}-warn.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <fileNamePattern>${logRoot}/${logModule}-warn-%d{yyyy-MM-dd}.log.zip</fileNamePattern>
      <maxHistory>360</maxHistory>
    </rollingPolicy>
    <encoder>
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{50} [%file:%line] -%msg %n</pattern>
    </encoder>
  </appender>

  <!-- 所有级别的日志 -->
  <appender name="FILE_ALL" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${logRoot}/${logModule}-all.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <fileNamePattern>${logRoot}/${logModule}-all-%d{yyyy-MM-dd}.log.zip</fileNamePattern>
      <maxHistory>36</maxHistory>
    </rollingPolicy>
    <encoder>
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{50} [%file:%line] -%msg %n</pattern>
    </encoder>
  </appender>

  <!-- 应用配置 -->
  <root level="INFO">
    <appender-ref ref="FILE_WARN"/>
    <appender-ref ref="FILE_ALL"/>
  </root>
</configuration>

```

### Spring Boot配置
```yaml
logging:
  # 指定配置文件
  config: logback.xml
  file:
    # 指定日志输出目录
    path: /data/logs
```

### nginx配置
```conf
# 错误级别的日志输出
error_log  /data/logs  error;

http {
    # 日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '  
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    # 访问日志输出，指定输出目录和日志格式
    access_log  /data/logs  main; 
}
```

**日志变量说明：**
```php
$remote_addr ：记录访问网站的客户端地址
$remote_user ：记录远程客户端用户名称
$time_local ：记录访问时间与时区
$request ：记录用户的 http 请求起始行信息
$status ：记录 http 状态码，即请求返回的状态，例如 200 、404 、502 等
$body_bytes_sent ：记录服务器发送给客户端的响应 body 字节数
$http_referer ：记录此次请求是从哪个链接访问过来的，可以根据 referer 进行防盗链设置
$http_user_agent ：记录客户端访问信息，如浏览器、手机客户端等
$http_x_forwarded_for ：当前端有代理服务器时，设置 Web 节点记录客户端地址的配置，此参数生效的前提是代理服务器上也进行了相关的 x_forwarded_for 设置
```
