## 配置目录
上篇安装完之后的配置目录是：
```
/usr/local/nginx/conf
```
默认是/etc/nginx/nginx.conf，编译安装可以通过设置--conf-path=/etc/nginx/nginx.conf修改配置文件。

Nginx配置文件常见结构的从外到内依次是「http」「server」「location」等等，缺省的继承关系是从外到内，也就是说内层块会自动获取外层块的值作为缺省值。

## 配置
```
vim nginx.conf
```
### main
```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}
```
- worker_processes
定义worker进程的数量，这个最优的数值依赖于很多个因素，包括CPU的核数，保存数据的硬盘驱动数量，加载模式，一般是设置为CPU的核数（auto会自动检测cpu核数）。
- worker_connections
一个worker进程能打开的最大并发数连接。

具体可以参考[nginx官网main配置](http://nginx.org/en/docs/ngx_core_module.html)
### Http
```
http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    gzip  on;
    gzip_types  text/plain text/javascript application/x-javascript application/javascript text/css application/xml;
    gzip_vary on;

    server {}
}
```
具体可以参考[nginx官网http配置](http://nginx.org/en/docs/http/ngx_http_core_module.html#http)
### Server
接收请求的服务器需要将不同的请求按规则转发到不同的后端服务器上，在 nginx 中我们可以通过构建虚拟主机（server）的概念来将这些不同的服务配置隔离。
```
server {
    listen       80;
    server_name  localhost;
    root   html;
    index  index.html index.htm;
}
```
这里的 listen 指监听端口，server_name 用来指定IP或域名，多个域名对应统一规则可以空格分开，index 用于设定访问的默认首页地址，root 指令用于指定虚拟主机的网页跟目录，这个地方可以是相对地址也可以是绝对地址。

具体可以参考[nginx官网server配置](http://nginx.org/en/docs/http/ngx_http_core_module.html#server)

### Location
每个 url 请求都会对应的一个服务，nginx 进行处理转发或者是本地的一个文件路径，或者是其他服务器的一个服务路径。而这个路径的匹配是通过 location 来进行的。我们可以将 server 当做对应一个域名进行的配置，而 location 是在一个域名下对更精细的路径进行配置。
以上面的例子，可以将root和index指令放到一个location中，那么只有在匹配到这个location时才会访问root后的内容：
```
location / {
        root   /data/www/host2;
        index  index.html index.htm;
    }
```

location 匹配规则
```
~      波浪线表示执行一个正则匹配，区分大小写
~*    表示执行一个正则匹配，不区分大小写
^~    ^~表示普通字符匹配，如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
=      进行普通字符精确匹配
```

具体可以参考[nginx官网location配置](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)

### 静态文件映射
访问文件的配置主要有 root 和 alias 两个指令。这两个指令的区别容易弄混。
#### alias
alias后跟的指定目录是准确的，并且末尾必须加 /
```
location /c/ {
        alias /a/;
    }
```
#### root
root后跟的指定目录是上级目录，并且该上级目录下要含有和location后指定名称的同名目录才行。

### 负载均衡
```
upstream myserver; {
    ip_hash;    
    server 10.1.1.1:8001;
    server 10.1.1.2:8002;
    server 10.1.1.3;
    server 1.1.1.4;
}
location / {
    proxy_pass http://myserver;
}
```
我们在 upstream 中指定了一组机器，并将这个组命名为 myserver，这样在 proxypass 中只要将请求转移到 myserver 这个 upstream 中我们就实现了在四台机器的反向代理加负载均衡。其中的 ip_hash 指明了我们均衡的方式是按照用户的 ip 地址进行分配。另外还有轮询、指定权重轮询、fair、url_hash几种调度算法。

### 配置目录管理
因为会包含多个server配置，避免nginx.conf太大太乱，一般使用include命令和conf.d目录把配置分开。
```
include /usr/local/nginx/conf/conf.d/*.conf;
```

### 总结
以上是最简单的通过 nginx 实现静态文件转发、反向代理和负载均衡的配置。在 nginx 中所有的功能都是通过模块来实现的，比如当我们配置 upstream 时是用 upstream 模块，而 server 和 location 是在 http core 模块，其他的还有流控的 limt 模块，邮件的 mail 模块，https 的 ssl 模块。
