<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-09-21 16:15:51
 * @LastEditors: Please set LastEditors
-->
+ 拉取镜像
`docker pull nginx:1.15`
+ 宿主机新建文件夹
    ~~~shell
    mkdir /home/html
    mkdir /home/nginx
    ~~~
+ 新增配置文件
  ~~~conf
    #user  nobody;
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
        ## pigx配置
        gzip  on;
        gzip_static on;
        gzip_min_length 1k;
        gzip_comp_level 4;
        gzip_proxied any;
        gzip_types text/plain text/xml text/css;
        gzip_vary on;
        gzip_disable "MSIE [1-6]\.(?!.*SV1)";

        server {
            listen       80;
            server_name  localhost;

            #charset koi8-r;

            #access_log  logs/host.access.log  main;

            location / {

                add_header Access-Control-Allow-Origin $http_origin;
                add_header Access-Control-Allow-Headers "Origin, No-Cache, X-Requested-With, If-Modified-Since, Pragma, Last-Modified, Cache-Control, Expires, Content-Type, X-E4M-With,userId,token,Access-Control-Allow-Headers";
                add_header Access-Control-Allow-Methods "POST, GET";
                add_header Access-Control-Max-Age "0";
                add_header Access-Control-Allow-Credentials "true";
                add_header XDomainRequestAllowed "1";



                root   /home/html/ui;
                index  index.html index.htm;
            }

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }

        }
    }
  ~~~
+ 创建容器
`docker run -idt --name nginx -p 80:80  -v /home/sdtzb/ui/dist/:/home/sdtzb/ui/dist/ -v /home/sdtzb/nginx/nginx.conf:/etc/nginx/nginx.conf -e LANG=en_US.UTF-8  nginx:1.15`