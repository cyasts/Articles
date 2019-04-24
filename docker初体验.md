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
**1. 卸载旧版的docker**
```
sudo yum remove docker docker-client \
	docker-client-latest docker-common \
	docker-latest docker-latest-logrotate \
	docker-logrotate docker-engine
```
/var/lib/docker 保留docker的图像，容器和网络所在的内容
**2.使用存储库安装**
1.安装依赖的包
```
sudo yum install -y yum-utils \
device-mapper-persistent-data \
lvm2
```
>yum-utils提供了yum-config-manager
>devide-mapper-persistent-date和lvm2需要devicemapper存储驱动程序

2.增加docker安装源
```
sudo yum-config-manager --add-repo \
  http://mirrors.ustc.edu.cn/dockerce/linux/centos/docker-ce.repo
```
这里后面的源网址可以自行更改

> 可选：启用夜间或测试存储库。
>
>这些存储库包含在docker.repo上面的文件中，但默认情况下处于禁用>>状态。您可以将它们与稳定存储库一起启用。以下命令启用夜间存储>>库。
>
>$ sudo yum-config-manager --enable docker-ce-nightly
要启用测试通道，请运行以下命令：
>
>$ sudo yum-config-manager --enable docker-ce-test
您可以通过运行带有标志的命令来禁用每晚或测试存储库 。要重新启用它，请使用该标志。以下命令禁用夜间存储库。yum-config-manager--disable--enable
>$ sudo yum-config-manager --disable docker-ce-nightly
3.
### 5.1.3 arch系
## 5.2 安装后的一些设置
## 5.3 使用教程