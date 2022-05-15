# Linux(CentOS7) PHP7 Oracle扩展安装pdo_oci.so oci8.so

首先去[Oracle官网](https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html)下载如下文件：

- oracle-instantclient11.1-basic-11.1.0.7.0-1.x86_64.rpm
- oracle-instantclient11.1-devel-11.1.0.7.0-1.x86_64.rpm
- oracle-instantclient11.1-sqlplus-11.1.0.7.0-1.x86_64.rpm

将两个文件移动至 /usr/lib/oracle 目录内

执行安装

```shell
$ rpm -ivh oracle-instantclient-xxx		#逐个安装
```

此时使用查找命令可查看Oracle客户端安装目录：

```shell
$ find / -name "client64" 	#/usr/lib/oracle/11.1/client64
```

添加环境变量

```shell
$ vim /etc/profile

export ORACLE_HOME=/usr/lib/oracle/11.1/client64
export ORACLE_BASE=/usr/lib/oracle/11.1
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
export PATH=$ORACLE_HOME/bin:$PATH

$ source /etc/profile  		#生效
```



使用 pecl 安装 oci8-2.2.0 扩展

```shell
$ pecl install oci8-2.2.0

#输入路径
instantclient,/usr/lib/oracle/11.1/client64/lib
```

安装期间会提示Oracle Instant Client目录，由于我们上面使用rpm安装，所以这里会自动识别，回车继续即可

此时将 **extension=oci8.so** 添加至 php.ini 文件中并重启即可完成 oci8 的扩展安装



由于PDO_OCI PHP官方的扩展库太久不维护，因此我们需要下载对应版本的PHP安装包获取安装包内 ext/pdo_oci 源文件进行手动编译扩展。

首先使用命令查看PHP版本号：

```shell
$ php -version		#PHP 7.2.34
```

本文是 7.2.34 于是使用如下链接下载版本文件：

```sh
$ wget https://www.php.net/distributions/php-7.2.34.tar.gz	#其他版本替换版本号
```

打开压缩包找到 /ext 目录内的 pdo_oci 文件夹，将其复制到服务器PHP的ext目录内，找不到的find查一下，本文的目录为 /usr/include/php/ext

```shell
$ vim /etc/ld.so.conf  
# 加入此行，保存退出
/usr/lib/oracle/11.1/client64/lib/

$ ldconfig
```

此时需要建立Oracle的软连接：

```shell
$ ln -s /usr/lib/oracle/11.1/client64 /usr/lib/oracle/11.1/client
$ ln -s /usr/include/oracle/11.1/client64 /usr/include/oracle/11.1/client
```

进入pdo_oci目录执行如下命令

```shell
$ /usr/bin/phpize
$ ./configure --with-pdo-oci=instantclient,/usr/lib/oracle/11.1/client64/lib/ --with-oci8=instantclient,/usr/lib/oracle/11.1/client64/lib
$ make && make install
```

注意编译后会给出 .so 文件的目录地址，文本地址为： /usr/lib64/php/modules/

然后修改此时新建配置文件**（注意不是php.ini）**，使用全路径作为扩展：

```shell
$ vim /etc/php.d/pdo_oci.ini  # pdo_oci.ini是新建的
extension=/usr/lib64/php/modules/pdo_oci.so  # 此处为pdo_oci.so的全路径，在编译后有展示
```

重启PHP服务即可，重启PHP-FPM命令为：

```shell
$ systemctl restart php-fpm.service
```

使用 php -m 可查看扩展是否正确安装。



本文所用到的资源：PHP7.2.34 与Oracle 11.1相关软件包 [下载地址](download/PHP7.2.34-Oracle 11.1.zip)