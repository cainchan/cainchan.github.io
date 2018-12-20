---
title: php集成Markdown
date: 2017-04-01
tags: 
 - PHP
 - Markdown
---

###### 1.php安装markdown扩展

```bash
composer require michelf/php-markdown
```
###### 2.php转化markdown为html

```php
use \Michelf\MarkdownExtra;
$my_html = MarkdownExtra::defaultTransform($my_text);
```
###### 3.页面加入语法高亮插件highlight.js

```html
<link rel="stylesheet" href="//cdn.bootcss.com/highlight.js/8.5/styles/default.min.css">
<script src="//cdn.bootcss.com/highlight.js/8.5/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```
参考资料：
1. [php-markdown](https://michelf.ca/projects/php-markdown/)
2. [语法高亮插件-highlightjs](https://highlightjs.org)