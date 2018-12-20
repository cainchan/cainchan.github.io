---
title: Nginx+Apache反向代理配置
date: 2017-03-01
tags: 
 - Nginx
 - Apache
---

###### Nginx反向代理配置 
```
server { 
    listen 80; 
    server_name linkpod.cn www.linkpod.cn; 
    location / { 
        proxy_pass http://linkpod.sinaapp.com; 
        proxy_redirect http://linkpod.sinaapp.com/ /; 
        proxy_set_header X-Real-IP $remote_addr; 
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
        access_log off; 
        error_log off; 
    } 
} 
```
###### Apache反向代理配置 
```
RewriteEngine On 
RewriteBase / 
RewriteRule ^(.*)$ http://linkpod.sinaapp.com/$1 [P]
```

###### Nginx负载均衡+反向代理配置
```
upstream shlnjr.com.cn {    
    server 115.28.237.124:8000;    
}  
server { 
    listen 80; 
    server_name shlnjr.com.cn www.shlnjr.com.cn; 
    location / { 
        proxy_pass http://shlnjr.com.cn; 
        access_log off; 
        error_log off; 
    }
}
```