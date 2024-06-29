+++
draft = false
author = "CPoet"
title = "在apache-tomcat下配置PHP环境"
date = "2018-12-05T00:19:32+08:00"
description = ""
tags = ["Linux", "Tomcat"]
categories = [
    "linux",
]
+++
## 前言
第一次接触tomcat就要增加PHP环境支持，tomcat主要是用来支持JAVAWEB的，在我看来是有一定的难度。但是因为某些需求，还是决定上网搜索相关教程。搜索后发现有这方面需求的人还挺多，教程也挺多。研究了一番，主要分成两种方法：\1、在服务器上安装PHP环境后，修改web.xml和content.xml文件，完成以后把PHP项目移动到特定的目录中就可以访问PHP项目，（遗憾的是，我配置了几次都没有成功。在访问的时候会被服务器上的JSP项目强制跳转。）。2、方法与第一种类似，在安装PHP环境后，只需要修改web.xml文件，就可以在网站根目录下任意位置访问PHP项目。显然这是一个真正意义上的JAVAWEB+PHP环境。
由于相关教程在网上已经非常详细，我就直接转载到自己的博客中。以便满足以后可能出现的需求。

## 第一种配置方法
1. 请移步到[http://www.cnblogs.com/cisum/p/7845028.html
](http://www.cnblogs.com/cisum/p/7845028.html
)阅读
2. 访问成功后，可以把index.php添加到默认入口文件。
添加方法：在conf下的web.xml中找到<welcome-file-list>标签，并在其中加入<FileName.php>。如下
```xml
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
		<welcome-file>index.php</welcome-file>
    </welcome-file-list>
```

## 第二种配置方法
1. 你需要下载PHP环境，并把PHP加入环境变量中。第一种配置方法安装PHP。
2. 配置tomcat支持php项目
下载jar包使tomcat支持php项目
下载地址：链接：https://pan.baidu.com/s/1F77MjMkw9qTHXXRT_IcwtQ  提取码：xcyd
下载以后将其解压，并把jar文件移到tomcat的lib目录下

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-va65egzN-1601773814969)(https://www.orbpi.cn/img/1802/30.jpg)]
接下来就是修改web.xml文件，找到<web-app>标签，并在其中加入如下代码：
```xml
<listener>
 
 <listener-class>php.java.servlet.ContextLoaderListener</listener-class>
 
 </listener>
 
 <servlet>
 
 <servlet-name>PhpJavaServlet</servlet-name>
 
 <servlet-class>php.java.servlet.PhpJavaServlet</servlet-class>
 
 </servlet>
 
 <servlet>
 
 <servlet-name>PhpCGIServlet</servlet-name>
 
 <servlet-class>php.java.servlet.fastcgi.FastCGIServlet</servlet-class>
 
 <init-param>
 
 <param-name>prefer_system_php_exec</param-name>
 
 <param-value>On</param-value>
 
 </init-param>
 
 <init-param>
 
 <param-name>php_include_java</param-name>
 
 <param-value>Off</param-value>
 
 </init-param>
 
 </servlet>
 
 <servlet-mapping>
 
 <servlet-name>PhpJavaServlet</servlet-name>
 
 <url-pattern>*.phpjavabridge</url-pattern>
 
 </servlet-mapping>
 
 <servlet-mapping>
 
 <servlet-name>PhpCGIServlet</servlet-name>
 
 <url-pattern>*.php</url-pattern>
 
 </servlet-mapping>
```

然后找到<welcome-file-list>节点，添加以下内容：
```xml
<welcome-file>index.php</welcome-file>
```

没出意外的话，现在PHP已经配置完成。

3. 重启tomcat服务

4. 测试PHP配置是否成功
在tomcat的webapps目录下新建test.php，并在以下加入以下内容：

```php
<?php echo phpinfo(); ?>
```

6. 如果访问不成功，可以尝试修改访问端口，如80。

## END
> 本文内容参考网上教程，如果疏漏的地方，敬请指正！