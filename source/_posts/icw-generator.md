---
title: icw generator使用方法
date: 2017-01-01
categories: Ifchange
tags: icw
---

#### 1.新建数据库.数据表 toh.accounts
#### 2.配置项目路径
`vim generator.php`
在`$_map`中增加一条`["数据库名" => ["数据库名","core文件前缀名","项目路径"]]`
```php
['toh' => array('toh', 'Toh', '/data/zhengkai.chen/toh')]
```
#### 3.添加数据库配置文件路径
`vim configs/config.php`
增加数据库名`toh`到循环载入列表
```php
foreach (array('contacts', 'cv', 'hunter', 'oa', 'mail', 'chance', 'cms', 'chinahr', 'passport', 'career', 'crawler','gsystem','green_liepin', 'clickstream', 'proxy', 'grab', 'icdc_1', 'icdc_allot', 'pay', 'package', 'grab_mail','toh') as $db){
    include dirname(__FILE__). sprintf('/database/%s.schema.php', $db);
}
```

#### 4.新建数据库配置文件
`vim configs/database/toh.schema.php`
```
<?php
$g_dao_map['toh']['accounts'] = array(
	'path'=>'account', //目录名
	'dao'=>'account', //dao文件名
	'equal_search_items' => "",
	'like_search_items'  => "",
	'like2_search_items' => '',
	'fetch_name_columns' => array(),
	'data_maps_many'     => array(),
	'data_maps_one'      => array(),
	'kv_data_maps_one'      => array(),
	'kv_data_maps_many'  => array(),
	'model'  =>'account/Model_account', // model文件路径名
	'has_one'    =>"",
	'has_many'   =>"",
	'foriegn_key' =>'',
	);
$g_controller_map['toh']['accounts'] =array(
    'path'                      => '',
    'SUGGEST'                   => FALSE,// 是否需要suggest
    'controller'                => 'account', //控制器名
    'LOGIC'                     => 'account/Logic_account', //logic文件路径名
    'ILIST_ITEM'                => array(),
    'ILIST_ITEM2'               => array(),
    'SEARCH_INTVAL_ITEM'        => array(),
    'SEARCH_ITEM'               => array(),
    'SEARCH_CONDITIONS'         => array(),
);
```
#### 5.生成controller
```bash
#php generator.php 数据库名 controllers 表名
php generator.php toh controllers accounts
```
#### 6.生成model
```bash
#php generator.php 数据库名 models 表名
php generator.php toh models accounts
```
#### 7.生成dao
```bash
#php generator.php 数据库名 daos 表名
php generator.php toh daos accounts
```