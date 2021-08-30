---
title: 使用Certbot安装Let's Encrypt免费SSL证书
date: 2018-12-20 00:37:17
tags: SSL
---
#### 1.安装certbot
```
sudo yum install certbot
```

#### 2.生成域名证书
```bash
certbot certonly --standalone -d youdomain.com -d www.youdomain.com
```

#### 3.配置Nginx SSL站点
```
server {
    listen 80;
    server_name www.youdomain.com youdomain.com;
    return 301 https://$server_name$request_uri;
}

server{
    listen 443 ssl;
    server_name www.youdomain.com youdomain.com;
    index index.html index.htm index.php default.html default.htm default.php;
    root  /home/wwwroot/www.youdomain.com;
    ssl on;
    ssl_certificate /etc/letsencrypt/live/youdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/youdomain.com/privkey.pem;

    ……
    }
```
其中`return 301 https://$server_name$request_uri;`是用来实现80端口到443端口的流量跳转的.
重启Ningx即可

#### 4.查看证书

可使用下列命令：

```shell
certbot certificates
```

就可以看到当前机器所有域名的证书情况，包括域名、 到期日、证书路径、私钥路径四条信息。

#### 5.更新证书
因Let’s Encrypt提供的免费证书有效期为90天,所以我们通过以下语句通过续期
```
certbot renew --quiet 
```
更新所有站点证书

#### 参考
- [Certbot](https://certbot.eff.org)
- [Let’s Encrypt](https://letsencrypt.org/)

