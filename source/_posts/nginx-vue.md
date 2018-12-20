---
title: Nginx+Vue配置
date: 2017-08-01
tags: 
 - Nginx
 - Vue
---

```nginx
upstream server-api{
    # api 代理服务地址
    server 127.0.0.1:3110;    
}
upstream server-resource{
    # 静态资源 代理服务地址
    server 127.0.0.1:3120;
}
server {
    listen       3111;
    server_name  localhost;      # 这里指定域名
    root /home/www/server-statics;
    # 匹配 api 路由的反向代理到API服务
    location ^~/api/ {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://server-api;
    }
    # 假设这里验证码也在API服务中
    location ^~/captcha {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://server-api;
    }
    # 假设你的图片资源全部在另外一个服务上面
    location ^~/img/ {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://server-resource;
    }
    # 路由在前端，后端没有真实路由，在路由不存在的 404状态的页面返回 /index.html
    # 这个方式使用场景，你在写React或者Vue项目的时候，没有真实路由
    location / {
        try_files $uri $uri/ /index.html =404;
    }
}
```