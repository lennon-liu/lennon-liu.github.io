---
title: linux docker打包应用服务程序
cover: img/cplusplus/cover.jpg
tags: 
	- 基础知识
	- 打包
	- linux
categories: 
	- linux
---

### **一、HTTP服务部署到Docker容器中创建一个Dockerfile**

1. 选择基础镜像：选择一个适合您的应用程序的基础镜像，例如 Ubuntu 或 Alpine Linux，您可以在 Docker Hub 中找到许多可用的镜像。
2. 安装必要的软件：安装 C++ 编译器和所需的库，例如 boost 和 OpenSSL。
3. 将应用程序复制到容器中：将编译好的二进制文件或源代码复制到容器中。
4. 暴露端口：使用 EXPOSE 命令将容器内的 HTTP 服务端口（例如 10522）暴露给外部。
5. 设置启动命令：使用 CMD 命令设置容器启动时要运行的命令，例如启动您的 HTTP 服务。

下面是一个简单的Dockerfile示例：

```
FROM ubuntu:latest

# 安装必要的软件
RUN apt-get update && \
    apt-get install -y g++ \
                       libboost-all-dev \
                       openssl \
                       libssl-dev

# 创建一个工作目录并复制应用程序
WORKDIR /app
COPY my-http-server /app

# 暴露端口
EXPOSE 10522

# 设置启动命令
CMD ["./my-http-server"]
```

将上述Dockerfile保存到您的项目目录中，并在终端中导航到该目录。然后，运行以下命令以构建Docker镜像：

```
docker build -t my-http-server .
```

其中，“my-http-server”是您给镜像取的名称，最后的句点表示使用当前目录中的Dockerfile进行构建。

构建完成后，您可以运行以下命令以在容器中启动您的HTTP服务：

```shell
docker run -p 10522:10522 my-http-server
```

其中，“10522:10522”表示将容器内的端口10522映射到主机的10522端口，以便可以通过主机的IP地址和端口号访问您的HTTP服务。



### **二、在 CentOS 容器中预先部署 C++ HTTP 服务，并将其打包为 Docker 镜像，以便将其交付给用户使用**

1. 创建 CentOS 容器并登录：使用以下命令创建一个基于 CentOS 的 Docker 容器并登录：

```shell
docker run -it --name my-centos-container centos:latest
```

1. 在 CentOS 容器中安装必要的软件：在容器内使用 yum 包管理器安装 C++ 编译器和所需的库，例如 boost 和 OpenSSL：

```shell
yum install -y gcc-c++ boost boost-devel openssl-devel
```

1. 复制应用程序到容器中：将编译好的二进制文件或源代码复制到容器中，可以使用 Docker 命令将应用程序复制到容器中：

```shell
docker cp my-http-server my-centos-container:/app/
```

其中，“my-http-server”是您的应用程序文件所在的路径。

1. 在容器中运行应用程序并测试：使用以下命令在容器中运行您的应用程序：

```
perlCopy code
docker exec -it my-centos-container /app/my-http-server
```

确保您的 HTTP 服务在容器中正常运行，并且您可以使用 curl 命令或 Web 浏览器测试您的应用程序。

1. 保存容器状态为镜像：使用以下命令将容器保存为 Docker 镜像：

```shell
docker commit my-centos-container my-http-server-image
```

其中，“my-http-server-image”是您给镜像取的名称。

1. 导出 Docker 镜像文件：使用以下命令将 Docker 镜像导出为 tar 文件：

```shell
docker save my-http-server-image > my-http-server-image.tar
```

1. 将 Docker 镜像交付给用户：将 tar 文件发送给用户，以便他们可以通过 Docker 命令加载您的应用程序：

```shell
docker load < my-http-server-image.tar
```

用户现在可以使用以下命令在本地 Docker 环境中运行您的应用程序：

```shell
docker run -p 10522:10522 my-http-server-image /app/my-http-server
```

其中，“10522:10522”表示将容器内的端口10522映射到主机的10522端口，以便可以通过主机的 IP 地址和端口号访问您的 HTTP 服务。