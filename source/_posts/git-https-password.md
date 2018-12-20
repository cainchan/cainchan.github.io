---
title: Https方式使用Git设置密码的方式
date: 2017-07-01
tags: Git
---

https方式每次都要输入密码，按照如下设置即可输入一次就不用再手输入密码的困扰而且又享受https带来的极速

设置记住密码（默认15分钟）：
```bash
git config --global credential.helper cache
```
如果想自己设置时间，可以这样做：
```bash
git config credential.helper 'cache --timeout=3600'
```
这样就设置一个小时之后失效

长期存储密码：
```bash
git config --global credential.helper store
```