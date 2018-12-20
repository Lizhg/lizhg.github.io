---
layout: post
title: "如何使用Dockerfile"
categories: docker
---

## 关于Dockerfile
在构建镜像时，可以选择使用`Dockerfile`来构建。Docker会读取`Dockerfile`中的指令自动构建镜像。所有命令行中可以使用的Docker命令都能在`Dockerfile`中使用。

`docker build path`命令会在`path`目录下找到`Dockerfile`，然后执行`Dockerfile`中的指令；docker会先将当前环境发送到Docker daemon(Docker守护进程)，注意这里的当前环境指的是path目录，`.`代表当前目录。

大多数情况下，path最好是一个干净的文件夹，只包含Dockerfile以及其他构建所必需的文件。
```shell
# 在当前目录下,通过Dockerfile文件构建
➜ docker build .
Sending build context to Docker daemon  6.51 MB

# DockerFile也可以不在当前目录下,通过-f进行指定
➜ docker build -f /path/to/a/Dockerfile .
```

## 基本编写规则

* `Dockerfile`指令不区分大小写，但是为了更容易区分命令与参数，通常将命令大写
  ```shell
  # Comment
  INSTRUCTION arguments
  ```

* `FROM`指令会初始化构建环境并设置基础镜像，后续的指令都将在这个环境下得到执行。正因为如此，`Dockerfile`必须以`FROM`指令开头，唯一的例外是`ARG`指令。
  ~~~shell
  # 格式:
  # FROM <image> [AS <name>]
  # FROM <image>[:<tag>] [AS <name>]
  # FROM <image>[@<digest>] [AS <name>]

  # 指定centos为构建的基础镜像,默认获取最新的(latest)镜像
  FROM centos

  # 等同于FROM centos
  FROM centos:latest

  # 指定版本标签
  FROM centos:5
  ~~~

* `RUN`指令会在当前镜像上执行指定命令，并且将结果提交至一个新的镜像，供`Dockerfile`中指定下一步继续使用。
  ```shell
  # 格式:
  # RUN <command>
  # RUN ["executable", "param1", "param2"]

  RUN mkdir /data
  ```

* `CMD`指令，一个`Dockerfile`中只能存在一个`CMD`指令，如果存在多个的话，只有最后一个`CMD`指令会生效。
`CMD`的主要目的在于为容器启动后提供默认值的可执行命令或参数，也可以省略命令，不过在这种情况下需要指定`ENTRYPOINT`指令。
   ```shell
  # 格式:
  # CMD ["executable","param1","param2"] (exec form, this is the preferred form)
  # CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
  # CMD command param1 param2 (shell form)

  # CMD通过 /bin/sh -c 执行
  FROM ubuntu
  CMD echo "This is a test." | wc -

  # 通过 /usr/bin/wc 执行
  FROM ubuntu
  CMD ["/usr/bin/wc","--help"]
  ```

* `ENTRYPOINT`指令指定容器启动时执行的命令及参数。可以在使用`docker run`启动容器时通过加上`--entrypoint`对其进行覆盖。
  ```shell
  # 格式:
  # ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)
  # ENTRYPOINT command param1 param2 (shell form)

  FROM ubuntu
  ENTRYPOINT ["top", "-b"]
  CMD ["-c"]
  ```

* `EXPOSE`命令的作用是通知容器运行期间监听指定的网络端口，同时也可以指定传输协议，默认为TCP。
  ```shell
  # 格式:
  # EXPOSE <port> [<port>/<protocol>...]

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
  ```shell
  # ADD [--chown=<user>:<group>] <src>... <dest>
  # ADD [--chown=<user>:<group>] [<src>,... <dest>]
  # 添加文件到`WORKDIR`/relativeDir/
  ADD test relativeDir/

  # 添加文件到/absoluteDir/
  ADD test /absoluteDir/
  ```
注意：*src路径必须在构建镜像时指定的环境(路径)内部*


## 案例

基于centos搭建一个web环境
```shell
FROM centos:7
RUN yum install java-1.8.0-openjdk
```

## 引用

* [https://docs.docker.com/engine/reference/builder](https://docs.docker.com/engine/reference/builder)
