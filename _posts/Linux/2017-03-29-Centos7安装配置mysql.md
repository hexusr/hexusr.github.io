---
layout: post
title:  "CentOS7安装配置MySQL(MariaDB)及常用命令"
date:   2017-03-29
categories: Linux
---

# 一.安装
## 1.安装MariaDB

```
 # yum install mariadb mariadb-server
```

## 2. 启动MariaDB服务

```
# systemctl start mariadb
```

## 3. 设置MariaDB服务开机自动启动

```
# systemctl  enable  mariadb
```

## 4. 进行配置MariaDB

```
# mysql_secure_installation
```

执行后 会提示如下:

```
Enter current password for root (enter for none): 
```

第一次安装没有设置密码，按回车键，然后进行设置密码

执行

```
# mysql -u root -p
```
输入密码登录MariaDB

# 二. MySql常用命令

## 1. 创建新MySQL用户及用户权限管理

用户权限在MySQL中其实就是一张表，用户名密码，权限保存在mysql数据库中的user表中，

对用户账号、密码、权限的相关操作其实就是修改mysql.user表中对应的字段。

user表中host字段表示该用户可以从哪个地址登录MySQL数据库



user表中host列的值的意义

host|含义
----|-----
%  |            匹配所有主机
localhost |   localhost不会被解析成IP地址，直接通过UNIXsocket连接
127.0.0.1  |    会通过TCP/IP协议连接，并且只能在本机访问；
::1         |        ::1就是兼容支持ipv6的，表示同ipv4的127.0.0.1


### 1.1 创建新用户
```
create user  username@地址 identified by 'password';
```

创建一个只能本地登录的username账号 ，密码为password

```
create user  username@localhost identified by 'password';
```

### 1.2 删除用户

```
 delete from mysql.user where user='username';
```

### 1.3 设置密码

```
set password for username = password('******');
```

或者

```
update mysql.user set password=password('*****') where user='username';
```

### 1.4 查看用户权限
```
show grants for rlq
```

### 1.5 授予权限

格式  授予用户拥有数据库中某个表的某个权限


如果被授予权限的用户不存在，则会创建此用户(创建的用户的Host字段为%)


```
grant 权限, on 数据库.表名 to 用户名;
```


授予username 拥有整个MySQL中所有数据库的权限

```
 grant all on *.* to username;
```

授予username 拥有testdb数据库中查 删 增 改 权限
```
grant select, insert,update,delete on testdb.* to username; 
```

### 1.6 回收权限
```
revoek 权限 on 数据库.表名 from 用户名;
```

执行

```
flush  privileges
```

使更改立即生效。

权限表


权限	|说明
-----|-----
all	 |
alter |	 
alter routine	|使用alter procedure 和drop procedure
create	| 
create routine |	使用create  procedure
create temporary tables	|使用create temporary table
create  user	| 
create view	 |
delete	 |
drop	 |
execute	|使用call和存储过程
file	|使用select into outfile  和load data infile|
grant option|	可以使用grant和revoke
index|	可以使用create index 和drop index
insert|	 
lock tables	|锁表
process|	使用show full processlist
reload	|   使用flush
replication client	|服务器位置访问
replocation slave	|由复制从属使用
select	 |
show databases|	 
show view	 |
shutdown	|使用mysqladmin shutdown 来关闭mysql
super	 |
update	 |
usage	|无访问权限
 	 
 	 
 
 


