//1 SSL功能需要openssl库，直接通过yum安装: 
yum install openssl
//2 gzip模块需要zlib库，直接通过yum安装: 
yum install zlib
//3 rewrite模块需要pcre库，直接通过yum安装: 
yum install pcre

## 安装依赖
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

## 安装nginx
yum install nginx



## nginx配置

```
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout  300s 300s;
    keepalive_requests 20000;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types.default;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    client_body_buffer_size 32K;
    client_header_buffer_size 128k;
    client_max_body_size 10m;
    large_client_header_buffers 4 128k;

    proxy_buffer_size 512k;
    proxy_buffers 16 512k;
    proxy_busy_buffers_size 1024k;
    proxy_temp_file_write_size 512k;
    server_tokens off;
    gzip  on;
    gzip_min_length 1k;
    gzip_buffers 4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 6;
    gzip_types text/plain application/javascript application/x-javascript text/javascript text/xml text/css image/jpeg image/gif image/png;
    gzip_disable "MSIE [1-6]\.";
    gzip_vary on;
    underscores_in_headers on;

    add_header X-Frame-Options SAMEORIGIN;
    add_header X-XSS-Protection "1; mode=block";
    add_header "X-Content-Type-Options" "nosniff";
    add_header "X-Content-Security-Policy" "default-src 'self'";
    add_header "X-Download-Options" "noopen";
    add_header "X-Permitted-Cross-Domain-Policies" "master-only";
    add_header "Content-Security-Policy" "default-rsc 'self'";
    add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";

   upstream idaas-api {
   		server 192.168.23.123:53003 max_fails=50 fail_timeout=10s;
                server 192.168.23.227:53003 max_fails=50 fail_timeout=10s;
		server 192.168.23.141:53003 max_fails=50 fail_timeout=10s;
		server 192.168.23.44:53003 max_fails=50 fail_timeout=10s;
		server 192.168.23.165:53003 max_fails=50 fail_timeout=10s;
		server 192.168.23.136:53003 max_fails=50 fail_timeout=10s;
		server 192.168.23.2:53003 max_fails=50 fail_timeout=10s;
		server 192.168.23.219:53003 max_fails=50 fail_timeout=10s;
        keepalive 500;
    }
    upstream idaas-sso {
        server 192.168.23.227:53007 max_fails=50 fail_timeout=10s;
		server 192.168.23.141:53007 max_fails=50 fail_timeout=10s;
		server 192.168.23.44:53007 max_fails=50 fail_timeout=10s;
		server 192.168.23.123:53007 max_fails=50 fail_timeout=10s;
		server 192.168.23.136:53007 max_fails=50 fail_timeout=10s;
		server 192.168.23.2:53007 max_fails=50 fail_timeout=10s;
		server 192.168.23.165:53007 max_fails=50 fail_timeout=10s;
		server 192.168.23.219:53007 max_fails=50 fail_timeout=10s;
	keepalive 500;
    }


   server {

        listen       8088;
        server_name  localhost;


        location / {
           root    /data/idaas/ui;
           index   index.html;
        }

        location /api/ {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header idaas-header 'api';
            proxy_pass http://idaas-api/;
            proxy_redirect http://$host/common/unauth http://$host:$server_port/api/common/unauth;
			proxy_set_header  X-Forwarded-Proto  $scheme;

        }


        location /sso/ {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header idaas-header 'sso';
            proxy_pass http://idaas-sso/;
            proxy_redirect http://$host http://$host:$server_port/sso;
            proxy_set_header  X-Forwarded-Proto  $scheme;
        }

        location /attachment/ {
            root    /data/idaas/;
            autoindex   on;
        }

    }
}
```



