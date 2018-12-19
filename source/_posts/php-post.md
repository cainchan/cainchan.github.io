---
title: PHP POST提交数据
date: 2016-12-19 21:21:06
tags: PHP
---
#### 1.封装curl方法:
```php
function curl($url,$post_data){
        $curl = curl_init(); 
        curl_setopt($curl, CURLOPT_URL, $url); 
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, true); 
        curl_setopt($curl, CURLOPT_POST, 1);//post提交方式
        curl_setopt($curl, CURLOPT_POSTFIELDS, $post_data);
        $data = curl_exec($curl);
        return $data;
}
```
#### 2.使用方法:
```php
$url = 'http://www.kaychen.cn/test';
$post_data = array(
        'timestamp'=>time(),
        'signature'=>$signature,
        'user_id'=>1,
);
$data = curl($url,$post_data);
```
