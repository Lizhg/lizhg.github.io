---
layout: post
title: "如何使用Dockerfile"
categories: docker
---

## 关于Dockerfile
Dockerfile是一个配置文件，所有在配置镜像中能使用的命令都能在Dockerfile中进行配置。Docker能读取Dockerfile中的指令自动构建镜像。

当指定`docker build path`命令时，docker会先将当前环境发送到Docker daemon(Docker守护进程)，注意这里的当前环境指的是path目录，`.`代表当前目录。

大多数情况下，path最好是一个干净的文件夹，只包含Dockerfile以及其他构建所必需的文件。


```shell
# 在当前目录下,通过Dockerfile文件构建
➜ docker build .
Sending build context to Docker daemon  6.51 MB

# DockerFile也可以不在当前目录下,通过-f进行指定
➜ docker build -f /path/to/a/Dockerfile .
```

## 编写Dockerfile

基本规则:

* 为了更容易区分命令与参数，通常将命令大写
```shell
INSTRUCTION arguments
```

* Dockerfile文件必须以`FROM`命令开始，并且指定一个基准镜像。`FROM <image>[:<tag>] [AS <name>]`

```shell
# redis为构建的基准镜像
FROM redis

# 等同于FROM redis
FROM redis:latest

# 指定版本标签
FROM redis:5.0
```

* `RUN`会在当前镜像上执行shell命令，并且将结果提交至一个新的镜像，供Dockerfile指定的后续步骤所使用。`RUN <command>`
```shell
RUN mkdir /data
```

* `CMD`/`ENTRYPOINT`


* `EXPOSE`命令的作用是通知容器运行期间监听指定的网络端口，同时也可以指定传输协议，默认为TCP。
`EXPOSE <port> [<port>/<protocol>...]`

```shell
# protocol不是tcp时，需要显示指定
EXPOSE 80/udp

# 同时指定tcp/udp
EXPOSE 80/tcp
EXPOSE 80/udp
```
如果要覆盖`EXPOSE`，可以在运行时通过`-p`指定
```shell
docker run -p 80:80/tcp -p 80:80/udp
```

* `ADD`命令将指定路径的文件或文件夹复制到**镜像**内指定路径下。
	+ `ADD [--chown=<user>:<group>] <src>... <dest>`
	+ `ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]`

```shell
# 添加文件到`WORKDIR`/relativeDir/
ADD test relativeDir/

# 添加文件到/absoluteDir/
ADD test /absoluteDir/
```
注意：*src路径必须在构建镜像时指定的环境(路径)内部*


## 引用

* [https://docs.docker.com/engine/reference/builder](https://docs.docker.com/engine/reference/builder)
