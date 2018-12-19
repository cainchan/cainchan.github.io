---
title: Composer笔记
date: 2016-12-19 21:17:51
tags: Composer
---
#### 1.安装
```bash
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```
#### 2.修改为中国镜像
1. 系统全局配置：即将配置信息添加到 Composer 的全局配置文件 `config.json` 中。
```bash
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

2. 单个项目配置： 将配置信息添加到某个项目的 `composer.json` 文件中。
```bash
composer config repo.packagist composer https://packagist.phpcomposer.com
```
上述命令将会在当前项目中的 composer.json 文件的末尾自动添加镜像的配置信息（你也可以自己手工添加）：
```
"repositories": {
    "packagist": {
        "type": "composer",
        "url": "https://packagist.phpcomposer.com"
    }
}
```

Composer中文指南：[phpcomposer.com](http://docs.phpcomposer.com/)