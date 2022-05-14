# Linux （CentOS7） 实用命令表

------------------------------------------

## 命令

查找文件

```shell
find / -name "xxxx*"		//全系统查找 xxxx 开头的文件
```

删除整个目录

```shell
rm -rf dir*		//删除 dir 目录及内部所有文件， -r 递归 -f 强制
```

移动一个文件/目录，也可用于重命名

```shell
mv a b			//a 移动到 b
```

重启

```shell
shutdown -r now		//立即重启
```

设置完全的读写权限

```shell
chmod 777 -R *		//给当前目录及子目录所有文件设置读写权限
```

Zip压缩与解压

```shell
zip -r xxxx.zip xxxxxx	//将xxxxxx目录及子目录文件压缩至xxxx.zip内
unzip xxxx.zip			//解压xxxx.zip
```

wget下载资源

```shell
wget https://download.redis.io/releases/redis-6.0.8.tar.gz	//下载到当前目录
```

Tar解压

```shell
tar -zxvf tarname.tar.gz	//解压到当前目录
```

## 防火墙firewall

```shell
firewall-cmd --list-all		//查看防火墙规则
firewall-cmd --reload		//加载规则，新配置的需要 reload 才能生效
firewall-cmd --add-port=3306/tcp --permanent	//开放 3306 端口
```