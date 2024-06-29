+++
draft = false
author = "CPoet"
title = "hexo安装链接转拼音出现的问题"
date = "2018-10-26T23:52:59+08:00"
description = ""
tags = []
categories = [
    "mixeds",
]
+++
## 出现的问题

如果说hexo安装了中文链接自动转拼音链接的话，可能会出现分类存在大写字母，那么在URL访问的时候不能访问到该分类的详情页。主要是因为该插件把网页中的url统一改成了小写，而hexo在生成分类的静态网页的时候是按照你填写的英文字母生成相同名的目录。例如：我存在Linux分类，显然在生成分类目录的时候，hexo会对应生成一个Linux的目录。但是在其他网页中，连接到Linux的链接会被插件改成linux。这可能会导致不能访问到Linux。

## 解决方法

1. 手动修改生成的目录，把Linux目录统一修改成linux。然后在把服务器上的Linux修改成linux，然后在把本地数据git到服务器即可正常访问。注意的是hexo在生成目录时，会把linux和Linux认定成一个目录，因此以后不需要做第二次修改。
2. 关闭服务器的大小写区别，关闭以后服务器会自动修正我们访问的url，如访问linux，虽然在网站目录下没有linux目录，但是服务器也会把Linux作为匹配放回给客户端。以apache为例：

    1. 加载mod_speling模块： LoadModule speling_module /usr/lib/apache2/modules/mod_speling.so

    2. 打开httpd.conf配置文件，找到#LoadModule speling_module /usr/lib/apache2/modules/mod_speling.so这一行，去掉前面的#号。
    开启模块： CheckSpelling on
    在httpd.conf后面加上CheckSpelling on

    3. 重启apache
    service httpd restart #重启apache服务器

hexo-permalink-pinyin(中文链接转拼音插件)

1. 安装

```shell
npm i hexo-permalink-pinyin --save
```

2. 开启插件

添加下面内容到配置文件_config.yml

```yaml
# https://github.com/viko16/hexo-permalink-pinyin
permalink_pinyin:
  enable: true
  separator: '-' # default: '-'
```

3. 相关选项
enable:是否启用插件
separator:词自己的间隔符

> ​参考：[GitHub - viko16/hexo-permalink-pinyin: A Hexo plugin which convert Chinese title to transliterate permalink.](https://github.com/viko16/hexo-permalink-pinyin)

​
