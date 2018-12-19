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