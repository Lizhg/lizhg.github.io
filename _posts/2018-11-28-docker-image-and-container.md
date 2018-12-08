---
layout: post
title: "Docker容器与镜像"
categories: docker
---
## 关于Docker
Docker是开发以及运维人员通过容器来开发，部署和运行应用程序的平台。<br>
使用Linux容器来部署应用程序的方式称为容器化，该方式有利于更轻松的部署应用程序。

### Docker镜像(Images)与容器(Containers)：
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

## 场景
搭建一个Web环境，其中Java版本要求为1.8，并且使用Tomcat9。根据这个要求我们首先需要获取到一个centos镜像，然后在基于该镜像搭建我们所需要的环境。

## 如何获取以及使用Docker镜像

1. 获取centos镜像
```shell
	# 在Docker Hub中搜索需要的镜像
	➜ docker search centos
	NAME                      DESCRIPTION                                     STARS   OFFICIAL   AUTOMATED
	centos                    The official build of CentOS.                   4890      [OK]
	ansible/centos7-ansible   Ansible on Centos7                              119                  [OK]
	jdeathe/centos-ssh        CentOS-6 6.10 x86_64 / CentOS-7 7.5.1804 x86…   99                   [OK]

	# 通常选择star较高的镜像
	➜ docker pull centos
```

2. 运行centos镜像
```shell
	# -d表示以Detached模式运行，-i表示即使不是attached???也保持标准输入打开，-t表示分配一个伪终端，--name表示容器名称，
	# --volume为挂载目录，-p将端口从容器绑定到宿主机上，这里是将容器的8080端口绑定到宿主机的8000端口
	➜ docker run -dit \
		--name my-centos \
		--volume /srv/centos/tmp:/tmp \
		-p 8000:8080 \
		centos /bin/bash
```

3. 安装Java
```shell
	# 安装OpenJDK 8
	➜ yum install java-1.8.0-openjdk

	# 验证
	➜ java -version
```

4. 安装Tomcat9
```shell
	# 安装到/usr/local目录下
	➜ cd /usr/local

	# 安装wget
	➜ yum install wget

	# 下载并解压到tomcat9文件夹
	➜ wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.12/bin/apache-tomcat-9.0.12.tar.gz
	➜ tar xzf apache-tomcat-9.0.12.tar.gz
	➜ mv apache-tomcat-9.0.12 tomcat9

	# 删除无用文件
	➜ rm -r apache-tomcat-9.0.12.tar.gz

	# 配置环境变量
	➜ echo "export CATALINA_HOME="/usr/local/tomcat9"" >> ~/.bashrc
	➜ source ~/.bashrc

	# 验证，启动成功后即可在宿主机上访问：http://localhost:8000/
	➜ cd /usr/local/tomcat9
	➜ ./bin/startup.sh
	Using CATALINA_BASE:   /usr/local/tomcat9
	Using CATALINA_HOME:   /usr/local/tomcat9
	Using CATALINA_TMPDIR: /usr/local/tomcat9/temp
	Using JRE_HOME:        /usr
	Using CLASSPATH:       /usr/local/tomcat9/bin/bootstrap.jar:/usr/local/tomcat9/bin/tomcat-juli.jar
	Tomcat started.
```

5. 为了方便以后重复使用这个环境，可以选择将该容器制作成镜像
```shell
	# base-os为仓库名，v1为标签
	➜ docker commit my-centos base-os:v1
	sha256:7d9c79fed1e506a1a232a8e72189a80e983fb4f38d5e5fc09755f085299bc23c

	# 查看镜像，其中base-os的运行方式同第二步，只需将镜像名调整为base-os
	# 用新镜像运行后的容器环境与我们在前面几步所搭建的环境一致。
	➜ docker images
	REPOSITORY  TAG      IMAGE ID       CREATED         SIZE
	base-os     v1       7d9c79fed1e5   5 seconds ago   459MB
	centos      latest   75835a67d134   1 hours ago     200MB
```

6. 停止容器
```shell
	# my-centos为第二步中定义了name参数，当然也可以使用container_id来代替name
	➜ docker stop my-centos
```

7. 再次启动容器
```shell
	➜ docker start my-centos
```