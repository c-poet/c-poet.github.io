---
weight: 0
title: "使用Spring-Security后，浏览器不能缓存的问题"
date: 2024-04-13T15:09:54+08:00
lastmod: 2024-04-13T15:09:54+08:00
draft: false
author: "CPoet"
authorLink: "https://www.cpoet.cn"
description: ""
featuredImage: ""

tags: ["spring", "spring-security", "缓存"]
categories: ["编程框架"]
---

{{< admonition type="tip" title="说明" >}}
Spring-Security在默认情况下是不允许客户端进行缓存的，在使用时可以通过禁用`Spring-Security`中的`cacheControl`配置项允许缓存。
{{< /admonition >}}


```java
protected void configure(HttpSecurity http) throws Exception {
        // 允许缓存配置
        http.headers().cacheControl().disable();
}
```
