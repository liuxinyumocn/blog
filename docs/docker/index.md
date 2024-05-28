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