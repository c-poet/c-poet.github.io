+++
draft = false
author = "CPoet"
title = "使用Spring-Security后，浏览器不能缓存的问题"
date = "2024-04-13T15:09:54+08:00"
description = "解决项目中使用SpringSecurity浏览器不能缓存问题"
tags = ["spring", "spring-security", "缓存"]
categories = [
    "framework/spring",
]
+++

> Spring-Security在默认情况下是不允许客户端进行缓存的，在使用时可以通过禁用`Spring-Security`中的`cacheControl`配置项允许缓存。

```java
protected void configure(HttpSecurity http) throws Exception {
        // 允许缓存配置
        http.headers().cacheControl().disable();
}
```
