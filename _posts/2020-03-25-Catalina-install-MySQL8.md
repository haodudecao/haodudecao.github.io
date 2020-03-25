---
layout: post
title: "Mac os Catalina 10.15.2 (19C57) 下安装 MySQL 8.0.9"
author: "HaoDu"
tags:
  - Mac
  - MySQL
---

## 前因
近日由于升级了 Catalina 系统，原有的LAMP 环境崩溃了，PHP 项目可能是因为在用户根目录，被强制迁移了；
## 过程
这里先记录下解决 MySQL 问题的过程 

1. 先尝试在 terminal 中启动 mysql

```
mysql -uroot 

ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
```
想到可能是服务没启动
2.  启动服务

```
mysql.server restart

. ERROR! The server quit without updating PID file (/usr/local/var/mysql/HDDC.local.pid).
```
3. 根据报错，在网上找到了一大堆解决方案，逐一试验，都没有用：

首先找到 [CSDN](https://blog.csdn.net/liyuxing6639801/article/details/84894656)，然后根据文中链接找到    [stackoverflow](https://stackoverflow.com/questions/9624774/after-mysql-install-via-brew-i-get-the-error-the-server-quit-without-updating)
乱七八糟都试过了，mysql 也卸载重装了（brew 安装好慢啊，一直失败，挂着梯子才能进行下去），还是不行……

```
brew remove mysql
brew cleanup
brew install mysql
```
其中有一条解决方案说是删除 *.err 文件
虽然没能解决问题，但是却是解决问题的引子
4. 发现错误日志 好奇就点进去看了看

```
tail -f /usr/local/var/mysql/HDDC.local.err
```
这下发现问题了，

```
2020-03-25T13:41:57.733840Z 0 [ERROR] [MY-000077] [Server] /usr/local/opt/mysql/bin/mysqld: Error while setting value 'NO_AUTO_VALUE_ON_ZERO,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,PIPES_AS_CONCAT,ANSI_QUOTES' to 'sql_mode'.
2020-03-25T13:41:57.734431Z 0 [ERROR] [MY-010119] [Server] Aborting
2020-03-25T13:41:57.734517Z 0 [Note] [MY-010120] [Server] Binlog end
2020-03-25T13:41:57.6NZ mysqld_safe mysqld from pid file /usr/local/var/mysql/HDDC.local.pid ended
2020-03-25T13:42:08.6NZ mysqld_safe Logging to '/usr/local/var/mysql/HDDC.local.err'.
2020-03-25T13:42:08.6NZ mysqld_safe Starting mysqld daemon with databases from /usr/local/var/mysql
mysqld: [Warning] World-writable config file '/usr/local/var/mysql/mysqld-auto.cnf' is ignored.
```
看意思是写入 sql_mode 失败，根据 error code MY-000077（这个很关键也很实用的技巧，错误码可以快速定位问题）
搜索发现解决方案是去 my.cnf 注释掉 sql_mod 行即可
5. 找到 my.cnf

```
find / -name my.cnf
/usr/local/etc/my.cnf
```
注释掉 sql_mode 行之后，错误日志还是不断更新（大概是因为设置了自动开启，系统不断自动尝试启动 MySQL）同样的错误。
6. 无奈之际，输错了 my.cnf 的路径


```
vim /etc/my.cnf
```
发现了这还有个 my.cnf 文件…… 注释 sql_mode 行，保存，查看错误日志，启动成功了……

```
2020-03-25T13:42:08.418164Z 0 [System] [MY-010116] [Server] /usr/local/opt/mysql/bin/mysqld (mysqld 8.0.19) starting as process 37272
2020-03-25T13:42:08.425096Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be an alias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2020-03-25T13:42:08.425741Z 0 [Warning] [MY-013244] [Server] --collation-server: 'utf8_unicode_ci' is a collation of the deprecated character set UTF8MB3. Please consider using UTF8MB4 with an appropriate collation instead.
2020-03-25T13:42:08.436172Z 0 [Warning] [MY-010159] [Server] Setting lower_case_table_names=2 because file system for /usr/local/var/mysql/ is case insensitive
2020-03-25T13:42:09.134463Z 1 [System] [MY-011090] [Server] Data dictionary upgrading from version '80012' to '80017'.
2020-03-25T13:42:10.562664Z 1 [System] [MY-013413] [Server] Data dictionary upgrade from version '80012' to '80017' completed.
2020-03-25T13:42:11.733092Z 4 [System] [MY-013381] [Server] Server upgrade from '80012' to '80019' started.
2020-03-25T13:42:15.186459Z 4 [System] [MY-013381] [Server] Server upgrade from '80012' to '80019' completed.
2020-03-25T13:42:15.341945Z 0 [Warning] [MY-000054] [Server] World-writable config file './auto.cnf' is ignored.
2020-03-25T13:42:15.342921Z 0 [Warning] [MY-010107] [Server] World-writable config file './auto.cnf' has been removed.
2020-03-25T13:42:15.343542Z 0 [Warning] [MY-010075] [Server] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 7022be42-6e9e-11ea-ba0d-728e718a6351.
2020-03-25T13:42:15.352004Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2020-03-25T13:42:15.356049Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/usr/local/var/mysql' in the path is accessible to all OS users. Consider choosing a different directory.
2020-03-25T13:42:15.381657Z 0 [System] [MY-010931] [Server] /usr/local/opt/mysql/bin/mysqld: ready for connections. Version: '8.0.19'  socket: '/tmp/mysql.sock'  port: 3306  Homebrew.
2020-03-25T13:42:15.696616Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/tmp/mysqlx.sock' bind-address: '::' port: 33060
```
正所谓
> 有心栽花花不开<br>
> 无心插柳柳成荫<br>
> 山重水复疑无路<br>
> 柳暗花明又一村

好诗好诗 