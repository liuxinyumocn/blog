# ubuntu LNPM安装与配置指引

## PHP7安装指引

#### 安装：

更新包管理器

```sh
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt install php7.2 php7.2-mysqli php7.2-pdo php7.2-curl php7.2-mbstring php7.2-gd
sudo apt install php7.2-fpm

```

#### 常用指令：

```shell
sudo systemctl start php7.2-fpm
sudo systemctl enable php7.2-fpm
```

```shell
sudo vim /etc/php/7.2/fpm/pool.d/www.conf
listen = 127.0.0.1:9000
sudo systemctl restart php7.2-fpm
```


## Nginx安装指引

#### 安装：

```shell
sudo apt install nginx
```

#### 常用指令：

```shell
systemctl enable nginx	#开机自动启动
systemctl start nginx	#启动nginx
systemctl status nginx	#检查运行状态
systemctl reload nginx	#重新生效配置文件（不停服务）
systemctl restart nginx	#重启nginx
systemctl stop nginx	#停止nginx
```

#### 配置：

##### 站点与SSL配置模板：

自行寻找 nginx.conf 文件位置，一般在 /etc/ 目录，或者 /usr/local/nginx/conf 目录

主要关注 http {  ...  } 方括号内配置修改，模板如下：

```
    #普通站点配置
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  www.domoe.cn;							#站点域名
        root         /usr/share/website/pxpro/public;		#站点根目录
        
	   # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
              if (!-e $request_filename) { 
                rewrite  ^(.*)$  /index.php?s=$1  last; 	#隐藏 .php 文件设置伪静态
                break; 
              } 
              index index.php index.html index.htm;
        }
        location ~ \.php$ {									#配置PHP-fpm
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
    
	# SSL站点配置
    server {
        listen       443 ssl http2 default_server;
        listen       [::]:443 ssl http2 default_server;
        server_name  www.domoe.cn;
	    root   /usr/share/website/pxpro/public;
        index index.php index.html index.htm;

        ssl_certificate "/usr/ca/1_pxpro.domoe.cn_bundle.crt"; 	#SSL证书crt文件路径
        ssl_certificate_key "/usr/ca/2_pxpro.domoe.cn.key"; 	#SSL证书key文件路径
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
              if (!-e $request_filename) { 
                rewrite  ^(.*)$  /index.php?s=$1  last; 
                break; 
              } 
              index index.php index.html index.htm;
        }
        location ~ \.php$ {
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
```

#### 同时支持index.php与index.htmlVue伪静态配置

```
	location / {
    	if (!-e $request_filename) { 
        	rewrite  ^(.*)$  /index.php?s=$1  last;
        	break; 
    	} 
    	index index.php index.html index.htm;
	}
	location ~* /static/ {
    	index index.html index.htm;
    	try_files $uri $uri/static/ /static/index.html;
	}
```



## MySQL(MariaDB) 数据库安装指引

#### 安装：

```sh
sudo apt install mariadb-server
```

#### 常见指令：

```shell
systemctl enable mariadb	#开机启动
systemctl start mariadb		#运行数据库 （开机启动不能代表立即运行）
systemctl stop mariadb		#停止
```

#### 初始化：

新安装数据库需要进行初始化配置，在启动数据库后执行：

```sh
sudo mysql_secure_installation
```

根据提示进行配置即可。

允许root用户远程访问

```shell
# 设置密码（不使用套接字） your_password 之前设置的密码
UPDATE mysql.user SET plugin = 'mysql_native_password', Password=PASSWORD('your_password') WHERE User='root' AND Host='localhost';

FLUSH PRIVILEGES;

# 允许所有IP访问
vim /etc/mysql/mariadb.conf.d/50-server.cnf
bind-address = 0.0.0.0
sudo systemctl restart mysql


mysql -u root -p 123456		#123456对应root用户的密码

#下面 123456 改为对应的密码
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

#刷新
FLUSH PRIVILEGES;

#退出
exit
```



# 端口检查

执行：

```shell
netstat -aptn
```

观察 80 、443、3306、9000 服务端口是否正常启动。