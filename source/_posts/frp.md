---
title: Frp内网穿透配置
date: 2021-08-30 23:00:00
tags:
 - Frp
---

#### 下载安装frp

在 Github 的 [Release](https://github.com/fatedier/frp/releases) 页面中下载到最新版本的客户端和服务端二进制文件，所有文件被打包在一个压缩包中

#### 部署

解压缩下载的压缩包，将其中的 frpc 拷贝到内网服务所在的机器上，将 frps 拷贝到具有公网 IP 的机器上，放置在任意目录

```BASH
tar xzf frp_0.37.0_linux_amd64.tar.gz
cd frp_0.37.0_linux_amd64
sudo cp -f frps frpc /usr/local/bin/
sudo mkdir /etc/frp
sudo cp -f frp* /etc/frp/
```

##### 配置服务端frps.ini

```
[common]
bind_port = 7000
vhost_http_port = 80
vhost_https_port = 443

dashboard_addr = 0.0.0.0
dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = admin
```

##### 生成域名证书（支持https）

```
certbot certonly  -d *.kaychen.cn --preferred-challenges dns --manual --server https://acme-v02.api.letsencrypt.org/directory
```

根据命令提示操作后，复制`fullchain.pem`和`privkey.pem`到`/etc/frp`目录



##### 配置客户端frpc.ini

```
[common]
server_addr = 43.128.56.179
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000

[jenkins]
type = https
custom_domains = jenkins.kaychen.cn
plugin = https2http
plugin_local_addr = 127.0.0.1:8081
plugin_crt_path = /etc/frp/fullchain.crt
plugin_key_path = /etc/frp/privkey.key
plugin_header_X-From-Where = frp

[gitlab]
type = https
custom_domains = gitlab.kaychen.cn
plugin = https2https
plugin_local_addr = 127.0.0.1:443
plugin_crt_path = /etc/frp/fullchain.crt
plugin_key_path = /etc/frp/privkey.key
plugin_header_X-From-Where = frp
```



##### 配置开机启动

客户端

vim `/lib/systemd/system/frpc.service`

```
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/frpc -c /etc/frp/frpc.ini
ExecReload=/usr/local/bin/frpc reload -c /etc/frp/frpc.ini

[Install]
WantedBy=multi-user.target
```

服务端

vim `/lib/systemd/system/frps.service`

```
[Unit]
Description=Frp Server Service
After=network.target

[Service]
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/frps -c /etc/frp/frps.ini

[Install]
WantedBy=multi-user.target
```

启动服务

```
sudo systemctl start frps
sudo systemctl enable frps

sudo systemctl start frpc
sudo systemctl enable frpc
```



#### 参考

- [FRP github](https://github.com/fatedier/frp)
- [frp下反向代理实现https协议](https://www.cnblogs.com/shook/p/12790532.html)

