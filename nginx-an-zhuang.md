# 基本安装

## 一、基础环境安装

### 安装make

```bash
yum -y install gcc automake autoconf libtool make
```

### 安装gcc++

```text
yum install gcc gcc-c++
```

## 二、Nginx安装

### 安装pcre

```text
cd /usr/local/src
wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.39.tar.gz 
tar -zxvf pcre-8.39.tar.gz
cd pcre-8.39
./configure
make
make install
```

### 安装zlib

```text
cd /usr/local/src
wget http://zlib.net/zlib-1.2.11.tar.gz
tar -zxvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure
make
make install
```

### 安装OpenSSL（不一定需要）

```text
cd /usr/local/src
wget https://www.openssl.org/source/openssl-1.0.1t.tar.gz
tar -zxvf openssl-1.0.1t.tar.gz
```

### 安装Nginx

```text
cd /usr/local/src
wget http://nginx.org/download/nginx-1.17.2.tar.gz
tar -zxvf nginx-1.17.2.tar.gz
cd nginx-1.17.2
./configure
make
make install
```

## 三、配置Nginx

### 代理前端资源

```text
[root@localhost ~]# cd /usr/local/nginx
[root@localhost nginx]# vim conf/nginx.conf

#user  nobody;
user admin;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen    7090;

        root /home/admin/ued/face;

        location  /  {
            try_files  $uri  @fallback;
        }

        location  @fallback  {
            rewrite  .*  /index.html  break;
        }

        location  /api/  {
            proxy_read_timeout    300;  #  Some  requests  take  more  than  30  seconds.
            proxy_connect_timeout 300;  #  Some  requests  take  more  than  30  seconds.
            proxy_set_header      X-Forwarded-Proto    $scheme;
            proxy_set_header      Host                 $host;
            proxy_set_header      X-Real-IP            $remote_addr;
            proxy_set_header      X-Forwarded-For      $proxy_add_x_forwarded_for;
            proxy_set_header      X-Forwarded-Ssl      on;
            proxy_set_header      X-Frame-Options      SAMEORIGIN;

            proxy_pass  http://192.168.110.34:7080/;
        }
    }

    server {
        listen    7091;

        root  /home/admin/ued/back;

        location  /  {
            try_files  $uri  @fallback;
        }

        location  @fallback  {
            rewrite  .*  /index.html  break;
        }

        location  /api/  {
            proxy_read_timeout    300;  #  Some  requests  take  more  than  30  seconds.
            proxy_connect_timeout 300;  #  Some  requests  take  more  than  30  seconds.
            proxy_set_header      X-Forwarded-Proto    $scheme;
            proxy_set_header      Host                 $host;
            proxy_set_header      X-Real-IP            $remote_addr;
            proxy_set_header      X-Forwarded-For      $proxy_add_x_forwarded_for;
            proxy_set_header      X-Forwarded-Ssl      on;
            proxy_set_header      X-Frame-Options      SAMEORIGIN;

            proxy_pass  http://192.168.110.34:7080/;
        }
    }
}
```

## 四、启动Nginx

### 测试Nginx配置文件

```text
./nginx -t
```

启动Nginx

```text
./nginx
```

刷新（重新加载）配置信息

```text
./nginx -s reload
```

