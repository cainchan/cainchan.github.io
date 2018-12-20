---
title: Laravel Class log does not exist报错解决办法
date: 2017-09-01
tags: 
 - PHP
 - Laravel
---

#### BUG1

Laravel Class log does not exist
在一个Laravel的项目下执行composer update，或者php artisan就是不成功，提示Uncaught ReflectionException: Class log does not exist，而根据提示的错误找不出原因。
报错内容如下：

```
PHP Fatal error:  Uncaught ReflectionException: Class log does not exist in /usr/share/nginx/html/tanteng.me/vendor/illuminate/container/Container.php:738
Stack trace:
#0 /usr/share/nginx/html/tanteng.me/vendor/illuminate/container/Container.php(738): ReflectionClass->__construct('log')
#1 /usr/share/nginx/html/tanteng.me/vendor/illuminate/container/Container.php(633): Illuminate\Container\Container->build('log', Array)
#2 /usr/share/nginx/html/tanteng.me/vendor/laravel/framework/src/Illuminate/Foundation/Application.php(687): Illuminate\Container\Container->make('log', Array)
#3 /usr/share/nginx/html/tanteng.me/vendor/illuminate/container/Container.php(853): Illuminate\Foundation\Application->make('log')
#4 /usr/share/nginx/html/tanteng.me/vendor/illuminate/container/Container.php(808): Illuminate\Container\Container->resolveClass(Object(ReflectionParameter))
#5 /usr/share/nginx/html/tanteng.me/vendor/illuminate/container/Container.php(777): Illuminate\Container\Container->getDependencies(Array, Array)
#6 /usr/share/nginx/htm in /usr/share/nginx/html/tanteng.me/vendor/illuminate/container/Container.php on line 738
```
后来`解决办法是安装mysqld的扩展`，是因为缺少这个扩展导致的异常。网上也有很多网友遇到类似的问题，但是其他原因导致的，如没有开启shor_open_tag，.env包含空格等。

参考链接：http://stackoverflow.com/questions/34978828/uncaught-reflectionexception-class-log-does-not-exist-laravel-5-2

laravel项目目录权限改成755 -R
且storeage目录权限改成777 -R



#### BUG2

laravel-wechat

```
composer require "overtrue/laravel-wechat"
```
安装参考:[laravel-wechat](https://github.com/overtrue/laravel-wechat/tree/3.1.10)

#### csrf排除wechat 
`vim Http/Middleware/VerifyCsrfToken :`
```
protected $except = [
        'wechat'
    ];

```