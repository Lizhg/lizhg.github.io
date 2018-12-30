---
layout: post
title: "Docker容器与镜像"
categories: docker
---
## 关于 Docker
Docker 是开发以及运维人员通过容器来开发，部署和运行应用程序的平台。<br>
使用 Linux 容器来部署应用程序的方式称为容器化，该方式有利于更轻松的部署应用程序。

### Docker 镜像与容器
首先，容器是通过运行镜像来启动的。而镜像则是一个可执行的包，里面包含所有运行这个应用所需要的代码，运行环境，库，环境变量以及配置文件等等，容器是镜像运行时的一个实例。<br>

### 基本命令
```shell
# 列出所有镜像
➜ docker images

# 列出所有容器
➜ docker container ls -a

# 列出运行中的容器
➜ docker ps

# 运行指定镜像
➜ docker run hello-world

# 停止运行容器
➜ docker stop container_id

# 移除本地容器
➜ docker rm contaienr_id

```

## 使用场景
假如我们要搭建一个 Web 环境，其中 Java 版本要求为 1.8，Tomcat 版本为 9。根据这个要求我们首先需要获取到一个 centos 镜像，然后在基于该镜像搭建我们所需要的环境。

1. 获取 centos 镜像
   ```shell
   # 在 Docker Hub 中搜索需要的镜像
   ➜ docker search centos
   NAME                      DESCRIPTION                                     STARS   OFFICIAL   AUTOMATED
   centos                    The official build of CentOS.                   4890      [OK]
   ansible/centos7-ansible   Ansible on Centos7                              119                  [OK]
   jdeathe/centos-ssh        CentOS-6 6.10 x86_64 / CentOS-7 7.5.1804 x86…   99                   [OK]

   # 通常选择官方镜像, 无官方镜像可以选择 star 数较高
   ➜ docker pull centos
   ```

2. 运行 centos 镜像
   ```shell
   # -d 表示以 Detached 模式运行，-i 表示即使不是 attached 模式也保持标准输入打开，-t 表示分配一个伪终端，--name 表示容器名称，
   # --volume 为挂载目录，-p 将端口从容器绑定到宿主机上，这里是将容器的 8080 端口绑定到宿主机的 8000 端口
   ➜ docker run -dit \
   	   --name my-centos \
       --volume /srv/centos/tmp:/tmp \
       -p 8000:8080 \
       centos /bin/bash
   ```

3. 进入容器
   ```shell
   ➜ docker exec -it my-centos /bin/bash
   ```

4. 安装 Java
   ```shell
   # 安装OpenJDK 8
   ➜ yum install java-1.8.0-openjdk

   # 验证
   ➜ java -version
   ```

5. 安装 Tomcat9
   ```shell
   # 安装 wget
   ➜ yum install wget

   # 安装到 /usr/local 目录下
   ➜ mkdir -p /usr/local/tomcat

   # 下载并解压到 tomcat9 文件夹
   ➜ wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.14/bin/apache-tomcat-9.0.14.tar.gz
   ➜ tar -xzf apache-tomcat-9.0.14.tar.gz -C /usr/local/tomcat --strip-components=1

   # 删除无用文件
   ➜ rm -f apache-tomcat-9.0.14.tar.gz

   # 验证，启动成功后即可在宿主机上访问：http://localhost:8000/
   ➜ /usr/local/tomcat/bin/startup.sh
   Using CATALINA_BASE:   /usr/local/tomcat9
   Using CATALINA_HOME:   /usr/local/tomcat9
   Using CATALINA_TMPDIR: /usr/local/tomcat9/temp
   Using JRE_HOME:        /usr
   Using CLASSPATH:       /usr/local/tomcat9/bin/bootstrap.jar:/usr/local/tomcat9/bin/tomcat-juli.jar
   Tomcat started.

   # 除了用 startup.sh 脚本启动, 也可以使用 catalina.sh 脚本启动
   ➜ /usr/local/tomcat/bin/catalina.sh run

   # ctrl + D 可以退出当前伪终端
   ```

6. `ctrl D` 退出伪终端

7. 如果以后需要重复使用这个环境，可以使用 `docker commit` 命令将该容器制作成镜像
   ```shell
   # 假如以 web 为仓库名，v1 为标签
   ➜ docker commit my-centos web:v1
   sha256:7d9c79fed1e506a1a232a8e72189a80e983fb4f38d5e5fc09755f085299bc23c

   # 查看镜像，其中 web 的运行方式同第二步，只需将镜像名调整为 web
   # 用新镜像运行后的容器环境与我们在前面几步所搭建的环境一致。
   ➜ docker images
   REPOSITORY  TAG      IMAGE ID       CREATED         SIZE
   web         v1       7d9c79fed1e5   5 seconds ago   459MB
   centos      latest   75835a67d134   1 hours ago     200MB
   ```

8. 停止容器
   ```shell
   # my-centos 为第二步中定义了 name 参数，当然也可以使用 container id 来代替 name
   ➜ docker stop my-centos
   ```

9. 启动容器
   ```shell
   ➜ docker start my-centos
   ```