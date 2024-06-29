+++
draft = false
author = "CPoet"
title = "Python使用requests发起请求体格式为multipart/form-data的请求，解决MISSING_ARGUMENTS"
date = "2020-10-15T22:25:18+08:00"
description = ""
tags = ["Python"]
categories = [
    "coding/python",
]
image = "20201015153442930.png"
+++

## Python使用requests发起请求体格式为multipart/form-data的请求

直接使用request可能遇见后端拿不到参数的情况，我遇见的错误为`error_message': 'MISSING_ARGUMENTS: api_key`。

![](20201015153442930.png)

解决方案是使用`requests_toolbelt`先处理数据格式，在使用`requests`进行请求。

```python
import requests as req
from requests_toolbelt.multipart.encoder import MultipartEncoder
import inc.config as conf
import base64
import io

img_file = io.open('../resource/img2.jpg', 'rb')
img_base64 = base64.encodebytes(img_file.read())

# 请求数据
req_data = {'api_key': conf.API_KEY, 'api_secret': conf.API_SECRET, 'image_base64': img_base64, 'return_attributes': 'age,gender'}

# 这里是重点
multipart = MultipartEncoder(req_data)
headers = {'Content-Type': multipart.content_type}

res = req.post(conf.faceApi.DETECT_API, req_data, headers=headers)

print(res.json())

img_file.close()
```
