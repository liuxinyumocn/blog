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

查看文件夹大小

```shell
du -sh		//查看当前文件夹大小
df -hl		//查看系统磁盘大小以及使用情况 请注意若有挂载磁盘 则业务要放到挂载磁盘目录内
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

## 互传文件

### 使用scp/rsync传输文件

该命令将被弃用，但其语法类似cp命令因此还是可以用的

如下命令在主机使用（非远程服务器），其中ip_address为远程服务器信息

```sh
$ scp username@ip_address:/home/username/filename .					#下载文件
$ scp filename username@ip_address:/home/username/filename			#上传文件

$ scp -r source_dir username@ip_address:/home/username/target_dir	#上传目录
```

该命令即将弃用可用 **rsync** 来替换

```shell
$ rsync username@ip_address:/home/username/filename .					#下载文件
$ rsync filename username@ip_address:/home/username/filename			#上传文件

$ rsync -r source_dir username@ip_address:/home/username/target_dir	#上传目录
```

### 使用sshfs构建远程目录实现文件传输

主机安装sshfs，Windows我不知道，因为Windows推荐使用类似Filezilla传输工具更方便，该方法适合在两个Linux服务器上互传文件

```shell
$ yum install sshfs
```

创建一个专门的远程目录

```shell
$ mkdir mount_dir
```

挂载

```shell
$ sshfs username@IP_address:path_to_dir mount_dir
```

这样将文件复制到该目录或者从该目录拿出来就跟本地计算机一样了

```shell
$ cp local_file mount_dir
```

使用完一定要卸载

```shell
$ umount mount_dir
```

## 防火墙firewall

```shell
firewall-cmd --list-all		//查看防火墙规则
firewall-cmd --reload		//加载规则，新配置的需要 reload 才能生效
firewall-cmd --add-port=3306/tcp --permanent	//开放 3306 端口
```



## SSH用户安全配置

配置Linux系统只允许特定用户进行远程登录

```shell
$ vim /etc/ssh/sshd_config

#添加下面信息 仅允许 root administrator 两个用户远程访问
AllowUsers root administrator

#重启ssh
$ service sshd restart
```

修改特定用户密码

```sh
$ passwd 用户名
```

