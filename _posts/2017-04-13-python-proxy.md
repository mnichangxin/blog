---
layout: post
title: "Python简单代理池"
categories: Python
tags: Python 代理 爬虫
---




Python简单代理池代码：

```python
    # -*- coding: utf-8 -*-
    class IPPOOLS():
        def __init__(self, ip = ''):
            '''初始化'''
            self.ip = ip
            def process_request(self, request, spider):
                '''使用代理ip，随机选用'''
                ip=random.choice(self.ip_pools) #随机选择一个ip
                print '当前使用的IP是' + ip['ip']
                try:
                    request.meta["proxy"] = "http://" + ip['ip']
                except Exception,e:
                    print e
                    pass
                ip_pools=[
                    {'ip': '124.65.238.166:80'},
                ]
 
```