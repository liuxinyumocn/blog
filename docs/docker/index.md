# Docker实用笔记

## 构建ubuntu基础镜像

Dockerfile:
```dockerfile
FROM ubuntu:20.04
LABEL maintainer="Nebulaliu <admin@liuxinyumo.cn>"
RUN apt-get update && apt-get install -y 
CMD ["/bin/bash"]
```

构建：
```sh
docker build -t my-ubuntu20-image .
```
其中 -t 指定了镜像的名称和标签，. 表示 Dockerfile 所在的当前目录。

## 进入镜像

```sh
docker run -it --name="my-ubuntu20-container" -p 8000:8000 .:/app/xxx my-ubuntu20-image
```
启动镜像 --name 指定容器名称， -v 绑定数据卷 -p 映射端口

## 将容器制作为镜像

```sh
docker commit [container ID] [image name]
```

例如：

```sh
docker commit my-ubuntu20-container my-image-0
```

## 发布到我的 Docker Hub

### 1.首先需要 login

```sh
docker login
```

也可以在 Docker Desktop 中手动登录。

### 2.打标签

```sh
docker tag base-php7:latest nebulaliu/base-php7:latest
```

### 3.上传

```sh
docker push nebulaliu/base-php7:latest 
```

## 私有仓库

### 创建
直接启动，并将仓库数据目录设置为本地 `/var/local/registry`

```sh
docker run -d -p 5001:5000 --restart=always -v /var/local/registry:/var/lib/registry  --name registry registry
```

### 发布至私有仓库

打tag
```sh
$ docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
$ docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu                            latest              ba5877dc9bec        6 weeks ago         192.7 MB
127.0.0.1:5000/ubuntu:latest      latest              ba5877dc9bec        6 weeks ago         192.7 MB
```

再push

```sh
$ docker push 127.0.0.1:5000/ubuntu:latest
The push refers to repository [127.0.0.1:5000/ubuntu]
373a30c24545: Pushed
a9148f5200b0: Pushed
cdd3de0940ab: Pushed
fc56279bbb33: Pushed
b38367233d37: Pushed
2aebd096e0e2: Pushed
latest: digest: sha256:fe4277621f10b5026266932ddf760f5a756d2facd505a94d2da12f4f52f71f5a size: 1568
```

配置非 https 推送权限

编辑 `/etc/docker/daemon.json` MacOS 在 Docker Desktop 中配置
```
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ],
  "insecure-registries": [
    "192.168.199.100:5000"
  ]
}
```