## nginx

### 概念介绍

#### 正向代理 

> 如果把局域网外的 Internet 想象成一个巨大的资源库，则局域网中的客户端要访
> 问 Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理。

#### 反向代理

> 其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只
> 需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返
> 回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器
> 地址，隐藏了真实服务器 IP 地址。

#### 负载均衡

> 将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器

#### 动静分离

>为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速
>度。降低原来单个服务器的压力。



### 相关命令

#### nginx

+ 启动 nginx
+ 关闭 nginx -s stop
+ 重新加载 nginx -s reload

### 配置文件

> conf ->  nginx.conf
>
> 包含三部分内容

#### 1 全局块

> 配置服务器整体运行的配置指令
>
> 比如 worker_processes 1 ; 处理并发数的配置

#### 2 events块

> 影响nginx服务器与用户的网络连接
>
> 比如 worker_connections 1024; 支持的最大连接数为 1024

#### 3 http块

```java
#文件扩展名与文件类型映射表
    include mime.types;
    #默认文件类型
    default_type application/octet-stream;
 
#日志相关定义
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    #定义日志的格式。后面定义要输出的内容。
    #1.$remote_addr 与$http_x_forwarded_for 用以记录客户端的ip地址；
    #2.$remote_user ：用来记录客户端用户名称；
    #3.$time_local ：用来记录访问时间与时区；
    #4.$request  ：用来记录请求的url与http协议；
    #5.$status ：用来记录请求状态； 
    #6.$body_bytes_sent ：记录发送给客户端文件主体内容大小；
    #7.$http_referer ：用来记录从那个页面链接访问过来的；
    #8.$http_user_agent ：记录客户端浏览器的相关信息
    #连接日志的路径，指定的日志格式放在最后。
    #access_log  logs/access.log  main;
    #只记录更为严重的错误日志，减少IO压力
    error_log logs/error.log crit;
    #关闭日志
    #access_log  off;
 
    #默认编码
    #charset utf-8;
    #服务器名字的hash表大小
    server_names_hash_bucket_size 128;
    #客户端请求单个文件的最大字节数
    client_max_body_size 8m;
    #指定来自客户端请求头的hearerbuffer大小
    client_header_buffer_size 32k;
    #指定客户端请求中较大的消息头的缓存最大数量和大小。
    large_client_header_buffers 4 64k;
    #开启高效传输模式。
    sendfile on;
    #防止网络阻塞
    tcp_nopush on;
    tcp_nodelay on;    
    #客户端连接超时时间，单位是秒
    keepalive_timeout 60;
    #客户端请求头读取超时时间
    client_header_timeout 10;
    #设置客户端请求主体读取超时时间
    client_body_timeout 10;
    #响应客户端超时时间
    send_timeout 10;
```

> 包括 http全局块, server块

+ http全局块

  > http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等

+ server块

  > 这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。
  > 每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。
  > 而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

#### 4 server

```
#虚拟主机定义
    server {
        #监听端口
        listen       80;
        #访问域名
        server_name  localhost;
        #编码格式，若网页格式与此不同，将被自动转码
        #charset koi8-r;
        #虚拟主机访问日志定义
        #access_log  logs/host.access.log  main;
        #对URL进行匹配
        location / {
            #访问路径，可相对也可绝对路径
            root   html;
            #首页文件。以下按顺序匹配
            index  index.html index.htm;
        }
 
#错误信息返回页面
        #error_page  404              /404.html;
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
 
#访问URL以.php结尾则自动转交给127.0.0.1
        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}
#php脚本请求全部转发给FastCGI处理
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}
 
#禁止访问.ht页面 （需ngx_http_access_module模块）
        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }
#HTTPS虚拟主机定义
    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;
    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
#vue配置
    server {
        listen       80;
        server_name  jcsd-cdn-monitor.jdcloud.com;
 
        #charset koi8-r;
 
        #access_log  logs/host.access.log  main;
 
        root /root/dist;
 
        location / {
            try_files $uri $uri/ /index.html;
        }
 
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```

### demo

#### 反向代理

```
server{
	listen 80;
	server_name	www.123.com;
	
	location / {
		# 设置代理
		proxy_pass	http://127.0.0.1:8080;
		index index.html index.jsp;
	}
}
```

