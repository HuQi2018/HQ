# 2019.07.11记录

[TOC]

## Docker 部署

### Ubuntu Docker：

1. 安装：wget -qO- https://get.docker.com/ | sh
	当要以非root用户可以直接运行docker时，需要执行 sudo usermod -aG docker runoob 命令，然后重新登陆
2. 启动docker：sudo service docker start
3. 测试运行hello-world：docker run hello-world
4. 镜像加速：（新版的 Docker 使用 /etc/docker/daemon.json（Linux） 或者 %programdata%\docker\config\daemon.json（Windows） 来配置 Daemon）
```
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

### Docker命令
1. 测试运行一个容器：docker run ubuntu:15.10 /bin/echo "Hello world"
	各个参数解析：
		• docker: Docker 的二进制执行文件。
		• run:与前面的 docker 组合来运行一个容器。
		• ubuntu:15.10指定要运行的镜像，Docker首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像。
		• /bin/echo "Hello world": 在启动的容器里执行的命令
		以上命令完整的意思可以解释为：Docker 以 ubuntu15.10 镜像创建一个新容器，然后在容器里执行 bin/echo "Hello world"，然后输出结果。
2. 运行交互式的容器：docker run -i -t ubuntu:15.10 /bin/bash
	各个参数解析：
		• -t:在新容器内指定一个伪终端或终端。
		• -i:允许你对容器内的标准输入 (STDIN) 进行交互。
	我们可以通过运行exit命令或者使用CTRL+D来退出容器
3. 启动容器（后台模式）：
	docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"（启动容器并在容器中运行相关命令）
4. 查看当前运行的容器状态：docker ps
5. 查看容器内的标准输出：docker logs [ CONTAINER ID | NAMES ]
6. 停止容器：docker stop [ CONTAINER ID | NAMES ]   （加-a参数可查看已停止的容器）
7. 启动停止的容器：docker start [ CONTAINER ID | NAMES ]
8. 重启正在运行的容器：docker restart [ CONTAINER ID | NAMES ] 
9. 移除已停止的容器：docker rm [ CONTAINER ID | NAMES ]
10. 运行一个web应用：
```
docker pull training/webapp  # 载入镜像
docker run -d -P training/webapp python app.py
参数说明:
	• -d:让容器在后台运行。
	• -P:将容器内部使用的网络端口映射到我们使用的主机上。
指定端口映射web应用容器：docker run -d -p 5000:5000 training/webapp python app.py

• -P :是容器内部端口随机映射到主机的高端口。
• -p : 是容器内部端口绑定到指定的主机端口。

• -v : 挂载本地目录至容器中的相关目录。
• ~/nginx/www:/usr/share/nginx/html

指定网络地址：(127.0.0.1)
docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py
指定传输类型：（udp）
docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
```
11. 容器命名：docker run -d -P --name runoob training/webapp python app.py
12. 查看 WEB 应用容器：docker ps
	PORTS
	0.0.0.0:32769->5000/tcp
	（Docker 开放了 5000 端口（默认 Python Flask 端口）映射到主机端口 32769 上，即主机可通过32769端口访问容器web页面。）
13. 查看到容器的端口映射：docker port [ CONTAINER ID | NAMES ]
14. 查看 WEB 应用程序日志：docker logs  [ CONTAINER ID | NAMES ]
15. 查看WEB应用程序容器的进程：docker top [ CONTAINER ID | NAMES ]
16. 检查 WEB 应用程序底层信息：docker inspect [ CONTAINER ID | NAMES ]
17. 查看最后一次创建的容器：docker ps -l
18. 查看本机已有镜像：docker images 
19. 获取一个新的镜像：docker pull ubuntu:13.10
20. 查找远程镜像：docker search httpd
	NAME:镜像仓库源的名称
	DESCRIPTION:镜像的描述
	OFFICIAL:是否docker官方发布
21. 更新镜像：
```
运行镜像：docker run -t -i ubuntu:15.10 /bin/bash
在镜像中更新环境：apt-get update

exit退出镜像后，使用commit更新到本地镜像文件：
docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2
各个参数说明：
	• -m:提交的描述信息
	• -a:指定镜像作者
	• e218edb10161：容器ID
	• runoob/ubuntu:v2:指定要创建的目标镜像名
我们可以使用 docker images 命令来查看我们的新镜像 runoob/ubuntu:v2： 
```
22. 构建镜像：
```
我们使用命令 docker build ， 从零开始来创建一个新的镜像。为此，我们需要创建一个 Dockerfile 文件，其中包含一组指令来告诉 Docker 如何构建我们的镜像。
构建我们的镜像。
runoob@runoob:~$ cat Dockerfile 
FROM    centos:6.7
MAINTAINER      Fisher "fisher@sudops.com"
RUN     /bin/echo 'root:123456' |chpasswd
RUN     useradd runoob
RUN     /bin/echo 'runoob:123456' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D
每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。
第一条FROM，指定使用哪个镜像源
RUN 指令告诉docker 在镜像内执行命令，安装了什么。。。
然后，我们使用 Dockerfile 文件，通过 docker build 命令来构建一个镜像。

docker build -t runoob/centos:6.7 .
参数说明：
	• -t ：指定要创建的目标镜像名
	• . ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

使用docker images 查看创建的镜像已经在列表中存在
```
23. 设置镜像标签：docker tag IMAGE ID REPOSITORY:tag
24. 进入指定容器：docker exec -it [ CONTAINER ID | NAMES ] /bin/bash 进入id号的shell

### [参考](https://www.runoob.com/docker/ubuntu-docker-install.html)


## Docker 部署 Nginx

### nginx 部署
1. 从官方拉取镜像：docker pull nginx
2. 创建Nginx容器：docker run --name runoob-nginx-test -p 8081:80 -d nginx (映射到本地8081端口)
3. 创建本地Nginx目录：mkdir -p ~/nginx/www ~/nginx/logs ~/nginx/conf
4. 拷贝容器内的文件到本地目录：docker cp 6dd4380ba708:/etc/nginx/nginx.conf ~/nginx/conf
	• www: 目录将映射为 nginx 容器配置的虚拟目录。 
	• logs: 目录将映射为 nginx 容器的日志目录。 
	• conf: 目录里的配置文件将映射为 nginx 容器的配置文件。
5. 映射目录：
重新部署一个Nginx容器并将本地目录映射到容器内(使用-v参数)：
docker run -d -p 8082:80 --name runoob-nginx-test-web -v ~/nginx/www:/usr/share/nginx/html -v ~/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/nginx/logs:/var/log/nginx nginx
启动以上命令后进入 ~/nginx/www 目录创建网页。
如果要重新载入 NGINX 可以使用以下命令发送 HUP 信号到容器：
$ docker kill -s HUP container-name

## Docker 部署 Nginx + PHP

### Nginx + PHP 部署
1. 下载镜像：docker pull php:5.6-fpm
2. 启动php容器（并映射到本地目录~/nginx/www）：
	docker run --name  myphp-fpm -v ~/nginx/www:/www  -d php:5.6-fpm
	命令说明：
		• --name myphp-fpm : 将容器命名为 myphp-fpm。
		-v ~/nginx/www:/www : 将主机中项目的目录 www 挂载到容器的 /www
3. 添加配置文件：
在该目录下添加 ~/nginx/conf/conf.d/runoob-test-php.conf 文件，内容如下：
```
server {
    listen       80;
    server_name  localhost;
location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm index.php;
    }
error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
location ~ \.php$ {
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /www/$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```
配置文件说明：
	• php:9000: 表示 php-fpm 服务的 URL，下面我们会具体说明。
	/www/: 是 myphp-fpm 中 php 文件的存储路径，映射到本地的 ~/nginx/www 目录。


4. 启动 nginx：
```
docker run --name runoob-php-nginx -p 8083:80 -d \
    -v ~/nginx/www:/usr/share/nginx/html:ro \
    -v ~/nginx/conf/conf.d:/etc/nginx/conf.d:ro \
    --link myphp-fpm:php \
    nginx
```
配置说明：	
	• -p 8083:80: 端口映射，把 nginx 中的 80 映射到本地的 8083 端口。
	• ~/nginx/www: 是本地 html 文件的存储目录，/usr/share/nginx/html 是容器内 html 文件的存储目录。
	• ~/nginx/conf/conf.d: 是本地 nginx 配置文件的存储目录，/etc/nginx/conf.d 是容器内 nginx 配置文件的存储目录。
	--link myphp-fpm:php: 把 myphp-fpm 的网络并入 nginx，并通过修改 nginx 的 /etc/hosts，把域名 php 映射成 127.0.0.1，让 nginx 通过 php:9000 访问 php-fpm。

5. 测试：
在 ~/nginx/www 目录下创建 index.php，代码如下：
<?php
echo phpinfo();
?>
最后通过 http://127.0.0.1:8083/index.php 网页代表配置成功。

## Docker 部署 MySQL

### 安装 MySQL

1. 下载mysql镜像：docker pull mysql:5.6
2. 创建相关目录：mkdir -p ~/mysql/data ~/mysql/logs ~/mysql/conf
	data目录将映射为mysql容器配置的数据文件存放路径
	logs目录将映射为mysql容器的日志目录
	conf目录里的配置文件将映射为mysql容器的配置文件
3. 创建mysql容器：
	docker run -p 3305:3306 --name mysql -v ~/mysql/conf:/etc/mysql/conf.d -v ~/mysql/logs:/logs -v ~/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
	命令说明：
		• -p 3306:3306：将容器的 3306 端口映射到主机的 3306 端口。
		• -v -v ~/mysql/conf:/etc/mysql/conf.d：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf。
		• -v ~/mysql/logs:/logs：将主机当前目录下的 logs 目录挂载到容器的 /logs。
		• -v ~/mysql/data:/var/lib/mysql ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 。
		-e MYSQL_ROOT_PASSWORD=123456：初始化 root 用户的密码。

### 通过 Dockerfile构建mysql镜像：
创建的mysql目录，创建Dockerfile
```
FROM debian:jessie
# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r mysql && useradd -r -g mysql mysql
# add gosu for easy step-down from root
ENV GOSU_VERSION 1.7
RUN set -x \
    && apt-get update && apt-get install -y --no-install-recommends ca-certificates wget && rm -rf /var/lib/apt/lists/* \
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture)" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture).asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true \
    && apt-get purge -y --auto-remove ca-certificates wget
RUN mkdir /docker-entrypoint-initdb.d
# FATAL ERROR: please install the following Perl modules before executing /usr/local/mysql/scripts/mysql_install_db:
# File::Basename
# File::Copy
# Sys::Hostname
# Data::Dumper
RUN apt-get update && apt-get install -y perl pwgen --no-install-recommends && rm -rf /var/lib/apt/lists/*
# gpg: key 5072E1F5: public key "MySQL Release Engineering <mysql-build@oss.oracle.com>" imported
RUN apt-key adv --keyserver ha.pool.sks-keyservers.net --recv-keys A4A9406876FCBD3C456770C88C718D3B5072E1F5
ENV MYSQL_MAJOR 5.6
ENV MYSQL_VERSION 5.6.31-1debian8
RUN echo "deb http://repo.mysql.com/apt/debian/ jessie mysql-${MYSQL_MAJOR}" > /etc/apt/sources.list.d/mysql.list
# the "/var/lib/mysql" stuff here is because the mysql-server postinst doesn't have an explicit way to disable the mysql_install_db codepath besides having a database already "configured" (ie, stuff in /var/lib/mysql/mysql)
# also, we set debconf keys to make APT a little quieter
RUN { \
        echo mysql-community-server mysql-community-server/data-dir select ''; \
        echo mysql-community-server mysql-community-server/root-pass password ''; \
        echo mysql-community-server mysql-community-server/re-root-pass password ''; \
        echo mysql-community-server mysql-community-server/remove-test-db select false; \
    } | debconf-set-selections \
    && apt-get update && apt-get install -y mysql-server="${MYSQL_VERSION}" && rm -rf /var/lib/apt/lists/* \
    && rm -rf /var/lib/mysql && mkdir -p /var/lib/mysql /var/run/mysqld \
    && chown -R mysql:mysql /var/lib/mysql /var/run/mysqld \
# ensure that /var/run/mysqld (used for socket and lock files) is writable regardless of the UID our mysqld instance ends up having at runtime
    && chmod 777 /var/run/mysqld
# comment out a few problematic configuration values
# don't reverse lookup hostnames, they are usually another container
RUN sed -Ei 's/^(bind-address|log)/#&/' /etc/mysql/my.cnf \
    && echo 'skip-host-cache\nskip-name-resolve' | awk '{ print } $1 == "[mysqld]" && c == 0 { c = 1; system("cat") }' /etc/mysql/my.cnf > /tmp/my.cnf \
    && mv /tmp/my.cnf /etc/mysql/my.cnf
VOLUME /var/lib/mysql
COPY docker-entrypoint.sh /usr/local/bin/
RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat
ENTRYPOINT ["docker-entrypoint.sh"]
EXPOSE 3306
CMD ["mysqld"]
```
通过Dockerfile创建一个镜像，替换成你自己的名字，运行命令，完成镜像构建：
docker build -t mysql .

## Docker网络安全实验室搭建：

### Docker 部署 sqli-labs

1. 查找sqli-labs 镜像：docker search sqli-lab 
2. 拉取镜像到本地：docker pull acgpiano/sqli-labs 
3. 查看已有的镜像：docker images
4. 运行容器：docker run -dt --name sqli -p 85:80 --rm acgpiano/sqli-labs
	-dt 让其在后台运行
	--name 给其命名
	-p 本地端口：docker中的端口 是将docker的端口映射到本地端口
	--rm 当其关闭后将删除开启的资源
5. 测试：sqli-labs安装完成，使用浏览器访问 http://127.0.0.1
	docker ps -a 显示容器container容器的id image 命令 端口等信息
	docker exec -it 2e49d3d9572a /bin/bash 进入id号的shell
### 同理dvwa
docker pull citizenstig/dvwa
docker run -dt --name dvwa -p 86:80 --rm citizenstig/dvwa

### 同理upload-labs
docker pull c0ny1/upload-labs
docker run -dt --name upload-labs -p 87:80 --rm c0ny1/upload-labs
