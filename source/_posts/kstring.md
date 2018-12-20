---
title: kstring linux下字符串批量查找替换工具
date: 2012-01-01
tags: kstring
---

2012年自制小工具分享: kstring

功能:

1.查找指定目录下所有存在指定字符串的文件

2.替换指定目录下所有文件中的指定字符串为新字符串

开发语言:python

部署:复制到/usr/bin/目录并给于执行权限.

用法:

查找字符串

kstring -f 目录路径 字符串

替换字符串

ksrting -r 目录路径 字符串 新字符串

———————————————————–

kstring -[f|r] dirpath string newstring

-f : find string in files
[ksrting -f dirpath string]
-r : replace string to newstring in files
[ksrting -r dirpath string newsrting]
-h : help
纯shell命令替换方法

sed -i “s/oldString/newString/g” `grep oldString -rl /path`

例如:

1.sed -i “s/大小多少/日月水火/g” `grep 大小多少 -rl /usr/aa`

2.sed -i “s/大小多少/日月水火/g” `grep 大小多少 -rl ./`