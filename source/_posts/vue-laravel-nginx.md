---
title: Nginx配置Vue和Laravel
date: 2019-11-1 14:00:00
tags:
 - Nginx
---

```nginx
upstream laowu {
	server 10.9.10.66:9000  fail_timeout=10 max_fails=5 weight=1 ;
	server 10.9.10.65:9000  fail_timeout=10 max_fails=5 weight=1 ;
}

server {
	listen       9090;
	server_name  laowu.ifchange.com;
	charset utf-8;
	root    /opt/wwwroot/toh/client/sly-web/dist;
	
	access_log  /opt/log/laowu.log main;
        error_log   /opt/log/laowu_err.log;	
        index  index.html index.htm index.php;

	location / {
       	try_files $uri $uri/ /index.html?$query_string;
  	}
	location /api {
		try_files $uri $uri/ /index.php?$query_string;
        }

	location ~ \.php$ {
		root /opt/wwwroot/toh/service/sly/public;
		fastcgi_pass            laowu ;
		fastcgi_index           index.php;
		fastcgi_split_path_info     ^(.+\.php)(.*)$;
		fastcgi_param       APP_ENV                production;
		fastcgi_param       PATH_INFO                $fastcgi_path_info;
		fastcgi_param       PATH_TRANSLATED        $DOCUMENT_ROOT$fastcgi_path_info;
		fastcgi_param       SCRIPT_FILENAME  $DOCUMENT_ROOT/$fastcgi_script_name;
		include             fastcgi_params;
	}
}
```

