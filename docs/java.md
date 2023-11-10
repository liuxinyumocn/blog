# CentOS7 Java 相关命令

## 安装
```sh
yum list java*

#检索1.8的列表
yum list java-1.8*

#安装1.8.0的所有文件
yum install java-1.8.0-openjdk* -y

#使用命令检查是否安装成功
java -version
```