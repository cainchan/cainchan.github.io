---
title: Ubuntu18.04部署Shadowsocks
date: 2018-12-18 21:21:50
categories:
- Internet
tags: 
- shadowsocks
- ss
---

#### 一.Server部署

1. 更新源 `sudo apt-get update`

2. 安装python-pip `sudo apt-get install python-pip`

3. 安装shadowsocks `sudo pip install shadowsocks`

4. 编写配置文件`sudo vim /etc/shadowsocks.json`

   ```
   {
   "server": "0.0.0.0",
   "server_port": 9999,
   "local_address": "127.0.0.1",
   "local_port": 5555,
   "password": "******",
   "method": "aes-256-cfb",
   "fast_open": true,
   "timeout":300
   }
   ```

5. 启动服务 `sudo ssserver -c /etc/shadowsocks.json -d start`


#### 二.客户端下载

[Client下载](https://github.com/shadowsocks/shadowsocks-windows/releases)

### 三.备注
* Ubuntu18.04需修正openssl版本问题,替换`EVP_CIPHER_CTX_cleanup`为`EVP_CIPHER_CTX_reset`

   `sudo sed -i 's/EVP_CIPHER_CTX_cleanup/EVP_CIPHER_CTX_reset/g' /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py`