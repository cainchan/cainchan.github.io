---
title: CentOS6 RabbitMQ体验
date: 2016-06-26
tags: 
 - RabbitMQ
 - PHP
 - CentOS
---

#### RabbitMQ安装与启动
```bash
#首先使EPEL生效(参考:http://fedoraproject.org/wiki/EPEL/FAQ#howtouse):
rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
#安装rabbitmq-server
yum install rabbitmq-server librabbitmq*
#启用rabbitmq-server
service rabbitmq-server start
#查看rabbitmq的插件列表
/usr/lib/rabbitmq/bin/rabbitmq-plugins list
#开启rabbitmq网页端管理功能
/usr/lib/rabbitmq/bin/rabbitmq-plugins enable rabbitmq_management
#关闭rabbitmq网页端管理功能
/usr/lib/rabbitmq/bin/rabbitmq-plugins disable rabbitmq_management
#使用默认账号密码(均guest)登陆http://server-name:15672/即可管理
```

#### PHP集成扩展ampq 
```
wget http://pecl.php.net/get/amqp-1.2.0.tgz #php5.3安装amqp1.2.0可成功. 1.4会报错
tar xzf amqp-1.2.0.tgz
cd amqp-1.2.0
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --with-amqp
make && make install
#增加生成的amqp.so到php.ini中
[AMPQ]
extension=/usr/local/php/lib/php/extensions/no-debug-non-zts-20090626/amqp.so
#重启web/php服务
/etc/init.d/nginx -s reload
#查看是否已集成成功
php -m|grep amqp
```
* 版本参考 `http://pecl.php.net/package/amqp`

#### PHP使用RabbitMQ的例子
入队列:

```php
'localhost' , 'port'=> '5672', 'login'=>'guest' , 'password'=> 'guest','vhost' =>'/');
$conn = new AMQPConnection($conn_args);
$conn->connect();
//创建exchange名称和类型
$channel = new AMQPChannel($conn);
$ex = new AMQPExchange($channel);
$ex->setName('direct_exchange_name');
$ex->setType(AMQP_EX_TYPE_DIRECT);
$ex->setFlags(AMQP_DURABLE | AMQP_AUTODELETE);
$ex->declare();
//创建queue名称，使用exchange，绑定routingkey
$q = new AMQPQueue($channel);
$q->setName('queue_name');
$q->setFlags(AMQP_DURABLE | AMQP_AUTODELETE);
$q->declare();
$q->bind('direct_exchange_name', 'routingkey_name');
//消息发布
$channel->startTransaction();
$message = json_encode(array('Hello World!','DIRECT'));
$ex->publish($message, 'routingkey_name');
$channel->commitTransaction();
$conn->disconnect();
?>
```

取队列:

```php
'localhost' , 'port'=> '5672', 'login'=>'guest' , 'password'=> 'guest','vhost' =>'/');
$conn = new AMQPConnection($conn_args);
$conn->connect();
//设置queue名称，使用exchange，绑定routingkey
$channel = new AMQPChannel($conn);
$q = new AMQPQueue($channel);
$q->setName('queue_name');
$q->setFlags(AMQP_DURABLE | AMQP_AUTODELETE);
$q->declare();
$q->bind('direct_exchange_name', 'routingkey_name');  
//消息获取
$messages = $q->get(AMQP_AUTOACK) ;
if ($messages){
      var_dump(json_decode($messages->getBody(), true ));
}
$conn->disconnect();
?>
```