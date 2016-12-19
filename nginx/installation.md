### 简介
Nginx是一款轻量级的网页服务器、反向代理服务器。相较于Apache、lighttpd具有占有内存少，稳定性高等优势。它最常的用途是提供反向代理服务。

> 在Centos下，yum源不提供nginx的安装，可以通过切换yum源的方法获取安装。也可以通过直接下载安装包的方法。
首先安装必要的库（nginx 中gzip模块需要 zlib 库，rewrite模块需要 pcre 库，ssl 功能需要openssl库）。选定/usr/local为安装目录，以下具体版本号根据实际改变。

### 下载安装
> 本文是CentOs 6.5环境，下面命令需要使用root账号

```
cd /usr/local
```

#### 安装pcre
```
wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.38.tar.gz
tar -xzvf pcre-8.38.tar.gz
cd pcre-8.38
./configure
make && make install
```

#### 安装zlib
```
wget http://zlib.net/zlib-1.2.8.tar.gz
tar -xzvf zlib-1.2.8.tar.gz
cd zlib-1.2.8
./configure
make && make install
```

#### 安装openssl
```
wget https://www.openssl.org/source/openssl-1.1.0c.tar.gz
tar -xzvf openssl-1.1.0c.tar.gz
cd openssl-1.1.0c
./config
make && make install
```

#### 安装nginx
```
wget http://nginx.org/download/nginx-1.11.7.tar.gz
tar -xzvf nginx-1.11.7.tar.gz
cd nginx-1.11.7
./configure --prefix=/usr/local/nginx --with-pcre=/usr/local/pcre-8.38 --with-zlib=/usr/local/zlib-1.2.8 --with-http_stub_status_module --with-http_ssl_module --with-openssl=/usr/local/openssl-1.1.0c --with-http_gzip_static_module
```
输入结果
```
Configuration summary
  + using PCRE library: /usr/local/pcre-8.38
  + using OpenSSL library: /usr/local/openssl-1.1.0c
  + using zlib library: /usr/local/zlib-1.2.8

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
```

```
make && make install
```

### 启动
```
/usr/local/nginx/sbin/nginx
```

访问ip和端口（例如localhost:80）出现Welcome to nginx!就是安装成功了。

### 其他命令
重启（重新加载配置文件）
```
/usr/local/nginx/sbin/nginx -s reload
```
快速停止
```
/usr/local/nginx/sbin/nginx -s stop
```
优雅停止（等待工作进程处理完当前请求）
```
/usr/local/nginx/sbin/nginx -s quit
```
测试配置文件是否正常
```
/usr/local/nginx/sbin/nginx -t
```

### 编译其他选项
```
--prefix=/etc/nginx
--sbin-path=/usr/sbin/nginx
--conf-path=/etc/nginx/nginx.conf
--error-log-path=/var/log/nginx/error.log
--http-log-path=/var/log/nginx/access.log
--pid-path=/var/run/nginx.pid
--lock-path=/var/run/nginx.lock
--http-client-body-temp-path=/var/cache/nginx/client_temp
--http-proxy-temp-path=/var/cache/nginx/proxy_temp
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp
--http-scgi-temp-path=/var/cache/nginx/scgi_temp
--user=nginx
--group=nginx
--with-http_ssl_module
--with-http_realip_module
--with-http_addition_module
--with-http_sub_module
--with-http_dav_module
--with-http_flv_module
--with-http_mp4_module
--with-http_gunzip_module
--with-http_gzip_static_module
--with-http_random_index_module
--with-http_secure_link_module
--with-http_stub_status_module
--with-http_auth_request_module
--with-threads
--with-stream
--with-stream_ssl_module
--with-http_slice_module
--with-mail
--with-mail_ssl_module
--with-file-aio
--with-http_v2_module
--with-ipv6
```
