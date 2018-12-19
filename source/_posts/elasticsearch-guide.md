---
title: ElasticSearch指南
date: 2018-12-19 21:39:07
tags: ElasticSearch
---
#### Why？(使用ES目标)
>解决根据progress和账号获取订单列表Mysql性能瓶颈

#### How?
>将每个订单的查询条件都存储到ES，进行索引，所有订单支持搜索的条件都添加到索引Mapping字段中去，（如何解决延迟问题？）

#### 一、安装
1. 依赖
  - Java
  - Node

2. 下载
  - [ElasticSearch官网下载](https://www.elastic.co/downloads/elasticsearch)
  - [ElasticSearch-Head GitHub下载](https://github.com/mobz/elasticsearch-head)

3. 部署ES
    1. 主节点配置文件`elasticsearch-master/config/elasticsearch.yml`添加
```
# 允许ElasticSearch-Head访问
http.cors.enabled: true
http.cors.allow-origin: "*"
# 设置集群名称
cluster.name: order
# 设置节点名称
node.name: master
# 设置为主节点
node.master: true
# 指定主机IP
network.host: 127.0.0.1
```

    2. 从节点配置文件`elasticsearch-slave/config/elasticsearch.yml`添加
```
# 设置集群名称
cluster.name: order
# 设置节点名称
node.name: slave01
# 指定端口
http.port: 9201
# 指定集群主节点IP
discovery.zen.ping.unicast.hosts: ["127.0.0.1"]
```

    3. 启动`./bin/elasticsearch -d`

4. 部署ES-HEAD
```
cd elasticsearch-head
npm install
npm run start
```
访问http://localhost:9100/


#### 二、使用

##### 1.创建索引
```
PUT http://127.0.0.1:9200/chengdan
{
    "settings":{
        "number_of_shards" : 1
    },
    "mappings":{
        "order":{
            "properties":{
                "order_id":{
                    "type":"integer"
                },
                "toh_account_id":{
                    "type":"integer"
                },
                "tob_account_id":{
                    "type":"integer"
                },
                "progress":{
                    "type":"integer"
                },
                "updated_at":{
                    "type":"date",
                    "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd"
                }
            }
        }
    }
}
```
##### 2.创建文档
```
POST http://127.0.0.1:9200/chengdan/order/34756359
{
    "order_id":34756359,
    "toh_account_id":10000002,
    "tob_account_id":1226,
    "progress":10,
    "updated_at":"2018-03-10 12:10:10"
}
```
##### 3.查询文档
```
POST http://127.0.0.1:9200/chengdan/order/_search
{
    "query":{
        "bool":{
            "must":[
                {"terms":{"toh_account_id":[10000001,10000001]}},
                {"terms":{"progress":[10,11,20]}}
            ]
        }
    },
    "sort":[
            {"updated_at":{"order":"desc"}}
    ]
}
```
##### 4.修改文档
```
POST http://127.0.0.1:9200/chengdan/order/34756359/_update
{
    "doc":{
        "progress":20,
        "updated_at":"2018-03-12 20:00:00"
    }
}
```