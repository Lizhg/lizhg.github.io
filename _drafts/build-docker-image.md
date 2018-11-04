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
`docker images`列出镜像，`docker ps`列出运行中的容器。


## 准备
1. docker search centos

2. docker pull centos

3. docker run -it \
	--name my-centos \
	--volume /srv/centos/tmp:/tmp \
	centos /bin/bash

进入my-centos容器
* 安装时需要用到wget
	yum install wget

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

* mysql5.7 https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/
	1. wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
	2. rpm -Uvh mysql80-community-release-el7-1.noarch.rpm
	3. yum repolist all | grep mysql
	4. yum-config-manager --disable mysql80-community
	5. yum-config-manager --enable mysql57-community
	6. yum repolist enabled | grep mysql
	7. yum install mysql-community-server
	8. service mysqld start
	10. service mysqld status
	11. mysql -uroot -p
	12. ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
