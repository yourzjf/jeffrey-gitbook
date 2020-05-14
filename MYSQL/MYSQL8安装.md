## MYSQL8.0安装

1、解压安装包 mysql-8.0.18-winx64.zip

2、拷贝到安装目录 D:\Program Files（`D:\Program Files\mysql-8.0.18-winx64`）

3、在MYSQL目录下定义my.ini文件（使用3307端口）

```properties
[mysqld]
port=3307
basedir="D:\Program Files\mysql-8.0.18-winx64"
datadir="D:\Program Files\mysql-8.0.18-winx64\data"
max_connections=200
max_connect_errors=10
character-set-server=UTF8MB4
default-storage-engine=INNODB
default_authentication_plugin=mysql_native_password
[mysql]
default-character-set=UTF8MB4
[client]
port=3307
default-character-set=UTF8MB4
```

4、以管理员身份运行CMD

5、进入mysql目录

```shell
>D:
>cd D:\Program Files\mysql-8.0.18-winx64\bin>
```

6、执行MYSQL安装：`mysqld --initialize --console`，此处会在控制台输出root的临时密码

```shell
D:\Program Files\mysql-8.0.18-winx64\bin>mysqld --initialize --console
2019-11-26T06:12:26.199968Z 0 [System] [MY-013169] [Server] D:\Program Files\mysql-8.0.18-winx64\bin\mysqld.exe (mysqld 8.0.18) initializing of server in progress as process 17052
2019-11-26T06:12:41.564033Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: W44SG:_u;Rd!
```

7、安装MYSQL服务，服务名：MYSQL8

```shell
D:\Program Files\mysql-8.0.18-winx64\bin>mysqld --install MYSQL8
Service successfully installed.
```

8、启动MYSQL服务

```shell
D:\Program Files\mysql-8.0.18-winx64\bin>net start MYSQL8
MYSQL8 服务正在启动 ..
MYSQL8 服务已经启动成功。
```

9、修改root密码，忽略警告

```shell
D:\Program Files\mysql-8.0.18-winx64\bin>mysqladmin -u root -p password root
Enter password: ************ #此处为安装时控制台打印的临时密码
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
Warning: Since password will be sent to server in plain text, use ssl connection to ensure password safety.
```

10、正常登录MYSQL服务

```shell
D:\Program Files\mysql-8.0.18-winx64\bin>mysql -uroot -p
Enter password: ****
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.18 MySQL Community Server - GPL

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## MYSQL5.7安装

MYSQL5.7安装与MYSQL8.0类似，MYSQL5.7的my.ini文件如下：

```properties
[mysqld]
port=3306
basedir=D:\Program Files\mysql-5.7.27-winx64
datadir=D:\Program Files\mysql-5.7.27-winx64\data
max_connections=200
character-set-server=utf8
default-storage-engine=INNODB
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
[mysql]
default-character-set=utf8
```

