---
title: mysql操作指南
date: 2016-12-19 21:22:29
tags: Mysql
---
#### 一、Mysql修改密码
###### 知道原密码
```sql
mysqladmin -u用户名 -p原密码 password 新密码
```
###### 不知道原密码
关闭数据库并以跳过授权表方式启动
```bash
mysqld_safe --skip-grant-tables &
```
启动之后进入数据库执行更新SQL
```sql
UPDATE mysql.user SET password=PASSWORD('新密码') WHERE User='root';
```

#### 二、Mysql授权
###### 1.远程授权
```sql
grant all on *.* to root@'%'identified by 'password';
```
###### 2.查看授权
```sql
show grants form root@'10.1.10.%'
```
###### 3.删除授权
```sql
revoke all on *.* from root@'%'; 
```
###### 4.刷新授权
```sql
flush privileges; 
```
###### 5.查看账号
```sql
select Db,Host from mysql.db; 
```
###### 6.查看当前账号
```sql
select user();
```
###### 7.根据条件删除账号
```sql
delete from mysql.db where Host="10.1.0.%";
delete from mysql.db where Db="dx7db_game01";
```

#### 三、Mysql日志
###### 1.查看binlog列表
```sql
show master logs;
```
###### 2.按名称来删除binlog
```sql
purge master logs to 'mysql-bin.001970';
```
###### 3.按时间来删除binlog
```sql
purge master logs before '2016-07-01 17:36:55';
```
#### 四、Mysql复制表
###### 1.此种方式在将表B复制到A时候会将表B完整的字段结构和索引复制到表A中来
```sql
CREATE TABLE A LIKE B
```
###### 2.此种方式只会将表B的字段结构复制到表A中来，但不会复制表B中的索引到表A中来
```sql
CREATE TABLE A AS SELECT x,x,x,xx FROM B LIMIT 0
```
这种方式比较灵活可以在复制原表表结构的同时指定要复制哪些字段，并且自身复制表也可以根据需要增加字段结构。 
两种方式在复制表的时候均不会复制权限对表的设置。比如说原本对表B做了权限设置，复制后，表A不具备类似于表B的权限。 留空，接下来会探讨两种复制表的性能