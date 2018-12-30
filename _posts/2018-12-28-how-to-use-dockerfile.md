---
layout: post
title: "使用Dockerfile构建镜像"
categories: docker
---

## 关于 Dockerfile
在构建镜像时，可以选择使用 `Dockerfile` 来构建。Docker 会读取 `Dockerfile` 中的指令自动构建镜像。所有命令行中可以使用的 Docker 命令都能在 `Dockerfile` 中使用。

`docker build path` 命令会在 `path` 目录下找到 `Dockerfile`，然后执行 `Dockerfile` 中的指令；docker 会先将当前环境发送到 Docker daemon(Docker 守护进程)，注意这里的当前环境指的是path目录，`.`代表当前目录。

大多数情况下，`path` 最好是一个干净的文件夹，只包含 `Dockerfile` 以及其他构建所必需的文件。
```shell
# 在当前目录下,通过 Dockerfile 文件构建
➜ docker build .
Sending build context to Docker daemon  6.51 MB

# DockerFile 也可以不在当前目录下,通过 -f 进行指定
➜ docker build -f /path/to/a/Dockerfile .
```

## 基本编写规则

* `Dockerfile` 指令不区分大小写，但是为了更容易区分命令与参数，通常将命令大写
  ```shell
  # Comment
  INSTRUCTION arguments
  ```

* `FROM` 指令会初始化构建环境并设置基础镜像，后续的指令都将在这个环境下得到执行。正因为如此，`Dockerfile` 必须以 `FROM` 指令开头，唯一的例外是 `ARG` 指令。
  ~~~shell
  # 格式:
  # FROM <image> [AS <name>]
  # FROM <image>[:<tag>] [AS <name>]
  # FROM <image>[@<digest>] [AS <name>]

  # 指定 centos 为构建的基础镜像,默认获取最新的(latest)镜像
  FROM centos

  # 等同于 FROM centos
  FROM centos:latest

  # 指定版本标签
  FROM centos:5
  ~~~

* `RUN` 指令会在当前镜像上执行指定命令，并且将结果提交至一个新的镜像，供 `Dockerfile` 中指定下一步继续使用。
  ```shell
  # 格式:
  # RUN <command>
  # RUN ["executable", "param1", "param2"]

  RUN mkdir /data
  ```

* `CMD` 指令，一个 `Dockerfile` 中只能存在一个 `CMD` 指令，如果存在多个的话，只有最后一个 `CMD` 指令会生效。
`CMD` 的主要目的在于为容器启动后提供默认值的可执行命令或参数，也可以省略命令，不过在这种情况下需要指定 `ENTRYPOINT` 指令。
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

* `ENTRYPOINT` 指令指定容器启动时执行的命令及参数。可以在使用 `docker run` 启动容器时通过加上 `--entrypoint` 对其进行覆盖。
  ```shell
  # 格式:
  # ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)
  # ENTRYPOINT command param1 param2 (shell form)

  FROM ubuntu
  ENTRYPOINT ["top", "-b"]
  CMD ["-c"]
  ```

* `EXPOSE` 命令的作用是通知容器运行期间监听指定的网络端口，同时也可以指定传输协议，默认为 TCP。
  ```shell
  # 格式:
  # EXPOSE <port> [<port>/<protocol>...]

  # protocol 不是 tcp 时，需要显示指定
  EXPOSE 80/udp

  # 同时指定tcp/udp
  EXPOSE 80/tcp
  EXPOSE 80/udp
  ```
  如果要覆盖 `EXPOSE`，可以在运行时通过 `-p` 指定
  ```shell
  docker run -p 80:80/tcp -p 80:80/udp
  ```

* `ADD` 命令将指定路径的文件或文件夹复制到**镜像**内指定路径下。
  ```shell
  # ADD [--chown=<user>:<group>] <src>... <dest>
  # ADD [--chown=<user>:<group>] [<src>,... <dest>]
  # 添加文件到 `WORKDIR`/relativeDir/
  ADD test relativeDir/

  # 添加文件到 /absoluteDir/
  ADD test /absoluteDir/
  ```
注意：*src 路径必须在构建镜像时指定的环境(路径)内部*


## 案例

基于 centos 搭建 tomcat 环境

1. 创建 `Dockerfile` 文件，完整内容如下：
   ```shell
   FROM centos:7

   ENV CATALINA_HOME /usr/local/tomcat
   ENV PATH $CATALINA_HOME/bin:$PATH
   WORKDIR $CATALINA_HOME

   RUN yum install -y java-1.8.0-openjdk \
     && yum install -y wget \
     && wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.14/bin/apache-tomcat-9.0.14.tar.gz \
     && mkdir -p "$CATALINA_HOME" \
     && tar -xzf apache-tomcat-9.0.14.tar.gz -C /usr/local/tomcat --strip-components=1 \
     && rm -r apache-tomcat-9.0.14.tar.gz \
     && yum clean all

   EXPOSE 80/tcp

   CMD ["catalina.sh", "run"]
   ```
   说明：
   * 当我们在终端上执行 `yum install` 命令时，会提示是否确认安装。但是我们在使用 `Dockerfile` 时，不会有类似的输入确认环节，所以我们可以通过在命令中指定 `-y` 自动确认以跳过该环节，否则将导致构建失败。
     ```shell
     # yum install wget
     # ...
     # Total download size: 38 M
     # Installed size: 122 M
     # Is this ok [y/d/N]:_

     ➜ yum install -y wget
     ```
   * 清理缓存和垃圾文件，避免镜像过于臃肿。
     ```shell
     ➜ rm -r apache-tomcat-9.0.14.tar.gz
     ➜ yum clean all
     ```

2. 在 `Dockerfile` 文件所在目录执行 `docker build -t web:v1 .`，其中 `web` 为镜像名称，`v1` 为版本号，`.` 指当前目录。
   ```shell
   ➜ docker build -t web:v1 .
   Sending build context to Docker daemon  2.048kB
   ...
   ...
   Successfully built 1354c6469ae9
   Successfully tagged web:v1

   ➜ docker images
   REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
   web                 v1                  6875af3e6d82        38 seconds ago      390MB
   centos              7                   1e1148e4cc2c        3 weeks ago         202MB
   ```

3. 验证镜像是否构建成功，执行以下命令后访问：http://localhost:8000/
   ```shell
   ➜ docker run -dit -p 8000:8080 web:v1
   ```

## 参考链接

* [Docker官方手册](https://docs.docker.com/engine/reference/builder)
