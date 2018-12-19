---
title: 使用Logstash实时分析日志
date: 2018-12-20 00:40:26
tags: Logstash
---
Logstash是一个应用程序日志、事件的传输、处理、管理和搜索的平台。你可以用它来统一对应用程序日志进行收集管理，提供 Web 接口用于查询和统计。

## 一、安装
环境:Centos6

需要java运行环境:`java -version`

下载并安装公共签名密钥
```bash
rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
```
然后在/etc/yum.repos.d/目录添加一个以`.repo`后缀的文件，比如:`logstash.repo`

```
[logstash-2.3]
name=Logstash repository for 2.3.x packages
baseurl=https://packages.elastic.co/logstash/2.3/centos
gpgcheck=1
gpgkey=https://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
```
至此资源库添加完成，你可以使用yum安装logstash了
```bash
yum install logstash
```
安装完成后，可用以下命令测试是否安装成功
```bash
bin/logstash -e 'input { stdin { } } output { stdout {} }'
```
logstash的默认程序路径在:`/opt/logstash/`

logstash的默认配置文件路径在:`/etc/logstash/conf.d/`

## 二、配置
在`/etc/logstash/conf.d/`新建一个以`.conf`后缀的文件，比如:`nginx.conf`
```
input {
    file {
        path => "/opt/log/pv.ifchange.com/pv.ifchange.com.access.log"
        type => "nginx-access"
    }
}
output {
    http {
        format => "form"
        http_method => "post"
        url => "http://logsystem.dev.ifchange.com/analysislog/clickLog"
        mapping => ["Body", '%{message}']
    }
    stdout {
        codec => "rubydebug"
    }
}
```
以上配置代表将文件`/opt/log/pv.ifchange.com/pv.ifchange.com.access.log`里的日志实时通过HTTP POST的方式提交给`http://logsystem.dev.ifchange.com/analysislog/clickLog`进行处理。为了方便调试，加了stdout参数，代表输出到屏幕。

##三、启动
为了方便调试可手动启动:
```bash
/opt/logstash/bin/logstash agent -f /etc/logstash/conf.d/test.conf
```
也可直接使用服务启动:
```bash
service logstash start
```
停止服务:
```bash
service logstash stop
```
配置文件测试:
```bash
service logstash configtest
```