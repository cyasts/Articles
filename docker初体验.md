---
title: 2019-4-23 docker初体验
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---
[toc]
# 1.docker是什么？
# 2.为什么会有docker？
# 3.什么时候什么人用到docker？
# 4.什么地方会用到docker？
# 5.怎么用docker？
## 5.1 安装docker-ce

### 5.1.1 debian系
### 5.1.2 redhat系
#### 1. 卸载旧版的docker
```
sudo yum remove docker docker-client \
	docker-client-latest docker-common \
	docker-latest docker-latest-logrotate \
	docker-logrotate docker-engine
```
/var/lib/docker 保留docker的图像，容器和网络所在的内容
#### 2.使用存储库安装
##### 1).安装依赖的包
```
sudo yum install -y yum-utils \
device-mapper-persistent-data \
lvm2
```
>yum-utils提供了yum-config-manager
>devide-mapper-persistent-date和lvm2需要devicemapper存储驱动程序

##### 2).增加docker安装源
```
sudo yum-config-manager --add-repo \
  http://mirrors.ustc.edu.cn/dockerce/linux/centos/docker-ce.repo
```
这里后面的源网址可以自行更改

> 可选：启用夜间或测试存储库。
>
>这些存储库包含在docker.repo上面的文件中，但默认情况下处于禁用>状态。您可以将它们与稳定存储库一起启用。以下命令启用夜间存储>>库。
>
>$ sudo yum-config-manager --enable docker-ce-nightly
要启用测试通道，请运行以下命令：
>
>$ sudo yum-config-manager --enable docker-ce-test
您可以通过运行带有标志的命令来禁用每晚或测试存储库 。要重新启用它，请使用该标志。以下命令禁用夜间存储库。yum-config-manager--disable--enable
>$ sudo yum-config-manager --disable docker-ce-nightly

##### 3).安装docker-ce
```
sudo yum install docker-ce docker-ce-cli containerd.io
```
如果提示接受秘钥，点接受即可。

如果要安装特定版本的docker-ce，先在repo中列出可用版本，然后选择安装。
```
$ yum list docker-ce --showduplicates | sort -r

docker-ce.x86_64  3:18.09.1-3.el7                     docker-ce-stable
docker-ce.x86_64  3:18.09.0-3.el7                     docker-ce-stable
docker-ce.x86_64  18.06.1.ce-3.el7                    docker-ce-stable
docker-ce.x86_64  18.06.0.ce-3.el7                    docker-ce-stable
```
然后使用
```
sudo yum install -y docker-ce-<version> docker-ce-cli-<version>...
```
完成安装。

**安装完成之后，docker用户组已经被创建，但没有用户被添加到docker用户组**
##### 4).启动docker
```sudo systemctl start docker```
如果要开机启动执行
```sudo systemctl enable docker```
### 5.1.3 arch系
## 5.2 安装完成后
### 1.测试是否正常安装了docker -ce
执行指令
```sudo docker run hello-world```
如果会正确的输出一条消息，即安装正确。
### 2.想以非root用户管理docker
>Docker守护程序绑定到Unix套接字而不是TCP端口。默认情况下，Unix套接字由用户拥有root，其他用户只能使用它sudo。Docker守护程序始终以root用户身份运行。


>如果您不想在docker命令前加上sudo，请创建一个名为docker的Unix组并向其添加用户。当Docker守护程序启动时，它会创建一个可由该docker组成员访问的Unix套接字。

>1.创建docker用户组
```sudo groupadd docker```
2.将用户添加到docker用户组
```sudo usermod -aG docker $username```
$username需要修改成相应的用户名
3.重启
4.验证是否可以运行docker
执行
```docker run hello-world```

### 3.设置docker开机启动
见前面
### 4.配置帧听ip地址和端口
### 5.启用ipv6
### 6.ip转发问题
### 7.为docker指定dns服务器
### 8.允许通过防火墙访问远程api
## 5.3 使用教程
### 1.基本操作
#### 1).查看docker版本
```docker --version```
#### 2).查看docker信息
```docker info```
或者
```docker version```
#### 3).列出计算机中的docker镜像
```docker image ls```
#### 4).构建应用程序
在Dockerfile所在目录内，创建好必要的文件配置，运行
```
docker build --tag=$imagename .
```
后面的`.`可以指定从哪个目录构建docker镜像
可选的：默认生成的镜像的标签为latest，如果要制定标签，使用
```docker build --tag=$imagename:v0.0.1 .```
#### 5).运行应用程序
```docker run -p ip:port $imagename```
这里前面的端口指定的是外部计算机的端口，后面的端口是容器内开放的端口，就是说把容器内部的80端口暴露到外部计算机的4000端口
#### 6).以后台模式运行应用程序
```
docker -d -p ip:port $imagename
```
#### 7).查看本地的docker容器
```docker container ls```
#### 8).停止正在运行的docker容器
```docker container stop $containerID```
#### 9).搜索远程仓库中的镜像
```docker search $imagename```
#### 10).
### 2.链接远程仓库
#### 1).注册docker账号
类似于github的远程仓库，需要推送到私人的仓库，所以需要在hub.docker.com注册一个账号，记住登录名和密码。
#### 2).在本地登录远程仓库
运行```docker login```然后输入账号和密码进行登录。
#### 3).将镜像推送到远程仓库
>不同于github，github是在本地初始化一个仓库，然后对每次的提交和更改对应一个标签，然后每次pull到远程仓库时，必须进入对应的远程仓库的目录，执行push remote ...，而docker是通过对镜像进行标记为username/repository:tag，然后直接针对有这种格式的标记进行推送。

首先需要对一个镜像进行标记，制定docker资源库的用户名和仓库名。
执行```docker tag $imagename username/repository:tag```对镜像进行标记。
例如```docker tag friendlyhello cyasts/get-started:part1```。注意这个时候还是在本地的，还没有推送到远程仓库，可通过```docker image ls```来查看本地的所有镜像。
然后推送到远程仓库。
执行```docker push username/repository:tag```即可推送到远程的仓库。**注意：推送的镜像会是公开的**
#### 4).从远程仓库拉取镜像
镜像推送到远程仓库后，就可以在任何关联了远程仓库的计算机上执行应用程序了。
```docker run -p 4000:80 cyasts/repository:tag```
如果本地镜像不可用，docker会从远程仓库中拉取到本地。这样就不需要安装python，以及以其他的依赖项了。
## 5.4 配置教程
>Dockerfile定义容器内环境中发生的事情。对网络接口和磁盘驱动器等资源的访问在此环境中进行虚拟化，该环境与系统的其他部分隔离，因此您需要将端口映射到外部世界，并具体说明要“复制”哪些文件到那个环境。但是，在执行此操作之后，您可以预期Dockerfile在此处定义的应用程序的构建 在其运行的任何位置都完全相同。

### 1.Dockerfile
#### 1).一个简单的例子
```
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```
这个dockerfile里面要求几个没有创建的文件，app.py和requirements.txt

接下来继续创建。
创建requirements.txt
```
Flask
Redis
```
创建app.py
```
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```
pip install -r requirements.txt为python安装flask和redis，应用程序打印变量NAME。
可以看到我们并没有安装python和flask，redis库。
#### 2).设置代理服务器
如果通过路由器或者代理服务器链接进入网络，而主机位于这些网络的后面，则代理服务器会阻止这些链接。可以通过指定代理服务器的ip和端口来解决。
```
ENV http_proxy host:port
ENV https_proxy host:port
```
### 2.docker-compose.yml
这个文件定义docker容易如何在生产中表现
注意这个文件中每个key: value对中，冒号后面必须有一个空格
```
version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image: username/repo:tag
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "4000:80"
    networks:
      - webnet
networks:
  webnet:
```
>此 docker-compose.yml 文件会告诉 Docker 执行以下操作：
>
>s从镜像库中拉取我们在步骤 2 中上传的镜像。
>
>将该镜像的五个实例作为服务 web 运行，并将每个实例限制为最多使>用 10% 的 CPU（在所有核心中）以及 50MB RAM。
>
>如果某个容器发生故障，立即重启容器。
>
>将主机上的端口 80 映射到 web 的端口 80。
>
>指示 web 容器通过负载均衡的网络 webnet 共享端口 80。（在内部，>容器自身将在临时端口发布到 web 的端口 80。）
>
>使用默认设置定义 webnet 网络（此为负载均衡的 overlay 网络）。

然后运行```docker swarm init``` ，这个命令是干啥的暂不清楚
然后执行```docker stack deploy -c **.yml applicationName```就可以部署成功了

### 3./etc/docker/daemon.json
#### 1).修改国内镜像源
docker的默认官方源，访问速度很慢，可以通过修改为国内源来提升速度。
在文件中加入
```
{
"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]
}
```
然后重启docker 守护进程。执行
```sudo systemctl restart docker```
#### 2).设置docker容器的dns地址
在文件中加入
```
{
"dns":["your_dns_address","8.8.8.8"]
}
```
然后重启docker守护进程
## 5.5 卸载docker ce
### 1.卸载docker包
执行指令：
```sudo yum remove docker-ce```
### 2.机器上的图片，容器和其他配置文件不会自行删除。
执行指令：
```sudo rm -rf /var/lib/docker```