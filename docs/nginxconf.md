# Nginx Conf 参考配置

------------------------------------------

```
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
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
    keepalive_timeout   65;
    types_hash_max_size 2048;
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    include /etc/nginx/conf.d/*.conf;

	#端口默认的请求配置
	server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  pxpro.domoe.cn;			#可配置默认的域名访问 如果是IP也会访问到这里
        root         /xxxx/xxxx;				#根目录地址
        
	   # Load configuration files for the default server block.
       include /etc/nginx/default.d/*.conf;

       location / {
       		if (!-e $request_filename) { 				#ThinkPHP5 的隐藏.php 伪静态配置
                rewrite  ^(.*)$  /index.php?s=$1  last; 
                break; 
             } 
             index index.php index.html index.htm;		#默认的文件访问优先级
        }
        location ~ \.php$ {								#配置PHP服务
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
    
    #对应的SSL服务配置	也是IP默认
	server {
        listen       443 ssl http2 default_server;
        listen       [::]:443 ssl http2 default_server;
        server_name  pxpro.domoe.cn;
	    root   /xxxx/xxxx;
        index index.php index.html index.htm;

        ssl_certificate "/xxxx/1_pxpro.domoe.cn_bundle.crt";	#证书CRT文件
        ssl_certificate_key "/xxxx/2_pxpro.domoe.cn.key";		#证书KEY
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;

		#后面与非SSL一样
        include /etc/nginx/default.d/*.conf;
        location / {
              #略
        }
    }
    
    #同一个端口根据不同域名的解析到不同的PHP服务目录
    server {
        listen       80;
        server_name  www.liuxinyumo.cn;					#别的域名
        root         /xxxxx/yyyyy;						#另一个PHP服务根目录
        
        #后面与非SSL一样
        include /etc/nginx/default.d/*.conf;
        location / {
              #略
        }
    }
    
    #同一个端口根据不同域名转发数据到不同的其他Web服务（反向代理）
    server {
    	listen		80;
    	server_name proxy.liuxinyumo.cn;				#被反向代理的域名
    	
        include /etc/nginx/default.d/*.conf;
        location / {
            proxy_set_header HOST $host;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://127.0.0.1:8080/;			#被代理的服务地址
        }
    }
}
```

重新加载配置文件命令：

```shell
$ systemctl reload nginx
```

另外记一次偶然的错误，如果发生 证书文件 提示无权限访问时，并检查并不是系统权限的问题，那很有可能是seLinux防火墙的问题，关闭指令：

```shell
$ setenforce 0
```

