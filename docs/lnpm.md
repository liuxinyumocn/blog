# CentOS7 LNPM安装与配置指引

## PHP7安装指引

#### 安装：

先配置PHP7 在centos7下的版本源

```sh
yum install epel-release -y
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

清除centos7历史残留PHP版本

```shell
yum -y remove php*
```

下载安装

```shell
yum -y install php72w php72w-cli php72w-fpm php72w-common php72w-devel php72w-embedded php72w-gd php72w-mbstring php72w-mysqlnd php72w-opcache php72w-pdo php72w-xml
```

#### 常用指令：

```shell
systemctl enable php-fpm.service	#开机启动
systemctl start php-fpm.service		#启动
```



## Nginx安装指引

#### 安装：

```shell
yum install nginx
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
yum -y install mariadb mariadb-server
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
mysql_secure_installation
```

根据提示进行配置即可。

允许root用户远程访问

```shell
mysql -uroot -p123456		#123456对应root用户的密码

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