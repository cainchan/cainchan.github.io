#### 申请生产环境部署

- web服务器:`192.168.7.41`,`192.168.7.42`
- mysql服务器:`192.168.7.43`(主),`192.168.7.45` (从)   端口:新开3308  (支持binlog读取)
- 测试域名: laowu.ifchange.com
- Nginx服务器:`192.168.7.39`
- 环境要求:  (Laravel5.5)>=PHP7.1, Nginx, Memcache

##### 仓库地址

- 后端业务项目地址:`git@gitlab.ifchange.com:toh/service/sly.git`  分支:`20191115`
- 后端框架项目地址:`git@gitlab.ifchange.com:toh/service/laravel-system.git`    分支:`publish`
- 前端业务项目地址: `git@gitlab.ifchange.com:toh/client/sly-web.git`    分支:`20191115`
- H5业务项目地址: `git@gitlab.ifchange.com:toh/client/sly-h5.git `    分支:`20191115`

##### Nginx配置:

1. Nginx配置

   ```nginx
   upstream laowu {
           server 192.168.7.41:9000  fail_timeout=10 max_fails=5 weight=1 ;
           server 192.168.7.42:9000  fail_timeout=10 max_fails=5 weight=1 ;
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
   		fastcgi_param       PATH_INFO                $fastcgi_path_info;
   		fastcgi_param       PATH_TRANSLATED        $DOCUMENT_ROOT$fastcgi_path_info;
   		fastcgi_param       SCRIPT_FILENAME  $DOCUMENT_ROOT/$fastcgi_script_name;
   		include             fastcgi_params;
   	}
   }
   ```

2. 定时脚本添加到192.168.7.42的Crontab

   1. 自动同步呼叫数据到业务端: `* * * * * php artisan schedule:run`

3.  服务器192.168.7.42添加后台进程:

   1. Excel异步解析进程: `php artisan queue:work --queue=excelParse --daemon &`

      需要默认支持服务重启，监听服务异常自动重启。

4. DDL(附件)

   1. 主库: `sly.sql`
   2. 外呼库: `sly_caller.sql`
   3. 人才库: `sly_talent.sql`