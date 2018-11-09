---
layout: post
title: "如何创建Docker镜像"
categories: docker
---
## 关于Docker
Docker是开发以及运维人员通过容器来开发，部署和运行应用程序的平台。<br>
使用Linux容器来部署应用程序的方式称为容器化，该方式有利于更轻松的部署应用程序。

### Docker镜像(Images)与容器(Containers)：
首先，容器是通过运行镜像来启动的。而镜像则是一个可执行的包，里面包含所有运行这个应用所需要的代码，运行环境，库，环境变量以及配置文件等等，容器是镜像运行时的一个实例。<br>

### 基本命令
``````
## 列出所有镜像
docker images`

## 列出所有容器
docker container ls -a

## 列出运行中的容器
docker ps

## 运行指定镜像
docker run hello-world

## 停止运行容器
docker stop container_id

## 移除本地容器
docker rm contaienr_id

``````

## 场景
搭建一个Web环境，其中Java版本要求为1.8，并且使用Tomcat9。

## 环境搭建

### 方案一：在centos镜像基础上自行搭建

1. 获取centos镜像
``````
	## 在Docker Hub中搜索需要的镜像
	～ docker search centos
	NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
	centos                             The official build of CentOS.                   4890                [OK]
	ansible/centos7-ansible            Ansible on Centos7                              119                                     [OK]
	jdeathe/centos-ssh                 CentOS-6 6.10 x86_64 / CentOS-7 7.5.1804 x86…   99                                      [OK]

	## 通常选择star较高的镜像
	～ docker pull centos
``````

2. 运行centos镜像
``````
	# -d表示以Detached模式运行，-i表示即使不是attached???也保持标准输入打开，-t表示分配一个伪终端，--name表示容器名称，--volume为挂载目录
	～ docker run -dit \
		--name my-centos \
		--volume /srv/centos/tmp:/tmp \
		centos /bin/bash
``````





* java
	1. yum -y list java*
	2. yum -y install java-1.8.0-openjdk*
	3. java -version

* tomcat: https://tecadmin.net/install-apache-tomcat-9-on-centos/
	1. cd /usr/local
	2. wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.12/bin/apache-tomcat-9.0.12.tar.gz
	3. tar xzf apache-tomcat-9.0.12.tar.gz
	4. mv apache-tomcat-9.0.12 tomcat9
	5. echo "export CATALINA_HOME="/usr/local/tomcat9"" >> ~/.bashrc
	6. source ~/.bashrc

* service
	1. yum list | grep initscripts
	2. yum install initscripts
