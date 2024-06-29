+++
draft = false
author = "CPoet"
title = "apache不能重定向，不能强制跳转https，-htaccess失效解决办法"
date = "2018-11-14T00:12:35+08:00"
description = ""
tags = []
categories = [
    "linux",
]
+++
> 解决apache服务器不能重定向，不能强制跳转https并且.htaccess失效等问题

## apache不能重定向，.htaccess被关闭
在centos系统下一键安装apache服务后，在网站根目录下建立.htaccess文件，想通过.htaccess文件实现apache的重定向。但是发现无论怎么更换.htaccess的代码，都不能重定向。这时候就需要检查.htaccess是否被关闭。
1. 打开apache的配置文件
```cmd
vim /etc/httpd/conf/httpd.conf
```
2. 检查配置文件
```cmd
AllowOverride Node      #338行附近
#修改为
AllowOverrlid All       #打开.htaccess
```
.htaccess打开后，还是不能访问可以检查文件名称是否正确。
```cmd
AccessFileName .htaccess        #409行附近，检查AccessFileName后面的文件名是否正确，可自定义
```
3. 修改完成后，需要重启服务器
```cmd
service https restart       #重启服务器 
```

## 实现强制跳转https
要想实现强制跳转https，一般是通过301重定向实现的。针对服务器管理员和网站管理员有以下不同的方法。
1. 在配置文件中增加跳转代码
```cmd
#在apache配置文件中增加下面代码
<VirtualHost *:80>
    ServerAdmin ASorb
    DocumentRoot /var/www/html/wordpress        #网站目录
    ServerName www.orbpi.cn         #修改为自己的域名
 
    RewriteEngine on
    RewriteCond   %{HTTPS} !=on
    RewriteRule   ^(.*)  https://%{SERVER_NAME}$1 [L,R]
</VirtualHost>
#添加后需要重启服务器
service httpd restart
```
2. .htaccess文件实现（使用前请按照上述方法确定.htsccess是否处于打开状态）
在网站根目录下建立.htaccess文件，将下面代码写入.htsccess文件中。( 重要提示：必须将代码放到.htaccess文件内容的最前面，以保证重定向优先权。)
```cmd
#代码如下：
RewriteEngine On
RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://www.orbpi.cn/$1 [R,L]
#或者
RewriteEngine On 
RewriteCond %{SERVER_PORT} 80 
RewriteRule ^(.*)$ https://www.orbpi.cn/$1 [R=301,L]
#又或者
RewriteEngine on
RewriteBase /
RewriteCond %{SERVER_PORT} !^443$
RewriteRule ^.*$ https://%{SERVER_NAME}%{REQUEST_URI} [L,R]

#注意：如果是在子目录，可以用
RewriteEngine On
RewriteCond %{SERVER_PORT} 80
RewriteCond %{REQUEST_URI} subfolder
RewriteRule ^(.*)$ https://www.orbpi.cn/subfolder [R,L]
```

## 效验是否成功跳转
检验是否能够重定向最简单的方法就是通过浏览器的访问，首先在浏览器中访问http://你的网址/，等待响应后，看网址是否变成https://你的网址/，如果发生改变就说明跳转成功，否则失败。有时候不能跳转可能不是服务器的配置问题，有可能是本地浏览器的缓存问题。例如谷歌浏览器就有缓存机制，所以在验证的时候可以先清除缓存在进行效验。

## END
> 参考：百度百科
